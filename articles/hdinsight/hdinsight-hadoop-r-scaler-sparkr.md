---
title: ScaleR i SparkR za pomocą usługi Azure HDInsight
description: ScaleR i SparkR za pomocą usługi uczenie Maszynowe na HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2017
ms.openlocfilehash: 78ea29b9b37c55a588a44f8d4b69486b89ee2fee
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684824"
---
# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>Łączenie programu ScaleR i SparkR w HDInsight

Ten dokument przedstawia sposób przewidywania usterek opóźnienia przybyciu locie przy użyciu **ScaleR** modelu regresji logistycznej. W przykładzie użyto dane pogody i opóźnienia lotów, łączone za pomocą **SparkR**.

Mimo że oba pakiety są uruchamiane na aparat wykonywania platformy Spark dla usługi Apache Hadoop, jest zablokowany danych w pamięci do udostępniania, ponieważ wymagają one każdego własne odpowiednich sesji platformy Spark. Dopóki ten problem został rozwiązany w przyszłych wersjach ML Server, obejście polega obsługa nienakładający sesji platformy Spark i wymieniać dane za pomocą plików pośrednich. Podanych tutaj instrukcji pokazują, że te wymagania są proste do osiągnięcia.

W tym przykładzie został początkowo udostępniony w dyskusji konferencji Strata 2016 Mario Inchiosa i Roni Burd. Można znaleźć tej dyskusji na [tworzenia skalowalnej platformy do analizy danych przy użyciu języka R](https://event.on24.com/eventRegistration/console/EventConsoleNG.jsp?uimode=nextgeneration&eventid=1160288&sessionid=1&key=8F8FB9E2EB1AEE867287CD6757D5BD40&contenttype=A&eventuserid=305999&playerwidth=1000&playerheight=650&caller=previewLobby&text_language_id=en&format=fhaudio).

Kod został pierwotnie napisane dla ML Server działający na platformie Spark w klastrze usługi HDInsight na platformie Azure. Ale koncepcji mieszanie użytkowania SparkR i programu ScaleR w jednym skrypcie również jest nieprawidłowy w kontekście dla środowisk lokalnych.

Kroki opisane w tym dokumencie założono, że poziom pośredni znajomości języka R oraz że [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-user-guide-introduction) biblioteki ML Server. Są wprowadzane do [SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html) podczas tego scenariusza.

## <a name="the-airline-and-weather-datasets"></a>Linie lotnicze i pogody zestawów danych

Dane lotów są dostępne z [archiwa dla instytucji rządowych USA](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236). Jest również dostępny jako zip z [AirOnTimeCSV.zip](https://packages.revolutionanalytics.com/datasets/AirOnTime87to12/AirOnTimeCSV.zip).

Dane o pogodzie, który można pobrać jako pliki zip w pierwotnej formie według: miesiąc, od [repozytorium National Oceanic i administrowania Atmospheric](https://www.ncdc.noaa.gov/orders/qclcd/). W tym przykładzie należy pobrać dane, maj 2007 — grudnia 2012. Używanie godzinowe plików danych i `YYYYMMMstation.txt` pliku w każdej aplikacji zips. 

## <a name="setting-up-the-spark-environment"></a>Konfigurowanie środowiska platformy Spark

Aby skonfigurować środowisko Spark, użyj następującego kodu:

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

Następnie dodaj `Spark_Home` do ścieżki wyszukiwania dla pakietów języka R. Dodanie go do ścieżki wyszukiwania można używać SparkR i zainicjować sesję SparkR:

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>Trwa przygotowywanie danych o pogodzie

Aby przygotować dane o pogodzie, podzbiór go do kolumny służące do modelowania: 

- "Widoczność"
- "DryBulbCelsius"
- "DewPointCelsius"
- "RelativeHumidity"
- "WindSpeed"
- "Altimeter"

Następnie dodaj kod lotniska, skojarzone z stację pogodową i przekonwertować pomiarów czasu lokalnego na UTC.

Rozpocznij od utworzenia pliku do mapowania informacji pogodowa (WBAN) zwróciło kod lotniska. Poniższy kod odczytuje każdego pliku co godzinę pogody nieprzetworzone dane, podzestawy do kolumn, firma Microsoft musi, scala pogodowa pliku mapowania, dostosowuje daty, godziny pomiarów względem czasu UTC, a następnie zapisuje nowej wersji pliku:

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>Importowanie dane pogody i linie lotnicze na elementy Dataframe aparatu Spark

Teraz możemy użyć SparkR [read.df()](https://docs.databricks.com/spark/1.6/sparkr/functions/read.df.html#read-df) funkcji, aby zaimportować dane pogody i linie lotnicze elementy Dataframe platformy Spark. Tej funkcji, takich jak wiele innych metod platformy Spark są wykonywane opóźnieniem, co oznacza, że są one w kolejce do wykonania, ale nie zostanie wykonany dopóki nie jest wymagane.

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>Czyszczenie danych i transformacji

Następnie wykonamy wyczyścić danych linii lotniczych, firma Microsoft zostały zaimportowane do zmiany nazwy kolumn. Tylko firma Microsoft zachować zmienne wymagane i zaokrąglanie godziny zaplanowanego wyjścia w dół do najbliższej godziny umożliwia scalanie przy użyciu najnowszych danych o pogodzie przy wyjściu:

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

Teraz wykonamy podobnych operacji na danych o pogodzie:

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>Łączenie danych o pogodzie i linie lotnicze

Używamy teraz SparkR [join()](https://docs.databricks.com/spark/1.6/sparkr/functions/join.html#join) funkcji lewe sprzężenie zewnętrzne dane pogody i linie lotnicze przez wyjścia AirportID i daty/godziny. Sprzężenie zewnętrzne pozwala zachować wszystkie rekordy linii lotniczych, nawet jeśli nie ma żadnych pasujących danych o pogodzie. Następujące sprzężenia firma Microsoft Usuń zbędne kolumny, a zmiana nazw kolumn zachowane, aby usunąć prefiks ramkę danych przychodzących, wynikające z sprzężenia.

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

W podobny sposób firma Microsoft dołączyć dane pogody i linie lotnicze w oparciu przybycia AirportID i daty/godziny:

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>Zapisz wyniki do pliku CSV do programu exchange przy użyciu programu ScaleR

Sprzężenia, co potrzeba zrobić za pomocą SparkR zostanie ukończona. Firma Microsoft zapisać dane z ostatnim Spark DataFrame "joinedDF5" do pliku CSV dla danych wejściowych do programu ScaleR, a następnie zamknąć sesji SparkR. Jawnie o SparkR można zapisać wynikowego pliku CSV w 80 oddzielnych partycjach umożliwiające wystarczające równoległości programu ScaleR przetwarzania:

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>Importowanie do XDF do użytku przez program ScaleR

Moglibyśmy użyć pliku CSV z dołączonym do linii lotniczych i dane pogodowe jako — dotyczy modelowania za pośrednictwem źródła danych programu ScaleR tekstu. Ale możemy zaimportować go do XDF po pierwsze, ponieważ jest bardziej wydajne, uruchamiając wiele operacji na zestawie danych:

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for ML Services 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>Podział danych na potrzeby szkolenia i testowania

Stosujemy rxDataStep rozdzielić dane 2012 do testowania i Zachowaj pozostałe dyski do szkolenia:

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>Nauczenia i przetestowania modelu regresji logistycznej

Teraz możemy przystąpić do zbudowania modelu. Aby zobaczyć wpływ danych o pogodzie opóźnienia w Godzina nadejścia, możemy użyć procedury regresji logistycznej przez program ScaleR. Zostanie użyty do modelowania, czy opóźnienie przybycia więcej niż 15 minut ma wpływ pogody na lotniskach wysyłki i przeznaczenia:

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

Teraz zobaczmy, jak ona działa na danych testowych dokonując pewnych prognoz i przeglądając ROC i AUC.

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>Ocenianie w innym miejscu

Możemy również użyć modelu oceniania danych na innej platformie. Zapisanie go do pliku pulpitu zdalnego i przesyłania, a następnie zaimportowanie tego usług pulpitu zdalnego do miejsca docelowego ocenianie środowiska takiego jak MIcrosoft SQL Server R Services. Jest to ważne upewnić się, czy poziomy współczynnik dane, które mają zostać ocenione odpowiadają na których został zbudowany modelu. Pasujący można osiągnąć, wyodrębnianie i zapisywanie informacji o kolumnie skojarzony z modelowania danych za pośrednictwem programu ScaleR w `rxCreateColInfo()` funkcji i następnie zastosowanie tej informacji o kolumnie do źródła danych wejściowych w celu prognozowania. W poniższym możemy zapisać kilka wierszy zestawu danych testowych i wyodrębnianie i informacji o kolumnie od tego przykładu w skrypcie prognozowania:

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>Podsumowanie

W tym artykule firma Microsoft została pokazuje, jak jest można połączyć użytkowania SparkR manipulowanie danymi za pomocą programu ScaleR opracowywania modelu w aparatu Spark usługi Hadoop. Ten scenariusz wymaga obsługi oddzielnych sesji platformy Spark, uruchamiania tylko jednej sesji w danym momencie i wymiany danych przy użyciu plików CSV. Mimo że jest to prosta, ten proces powinien być jeszcze łatwiejsze w nadchodzącej wersji usługi uczenie Maszynowe, gdy SparkR ScaleR można współużytkować sesja platformy Spark i dlatego udostępnianie elementy Dataframe platformy Spark.

## <a name="next-steps-and-more-information"></a>Kolejne kroki i dodatkowe informacje

- Aby uzyskać więcej informacji przy użyciu serwera usługi uczenie Maszynowe na platformie Apache Spark, zobacz [Przewodnik z wprowadzeniem](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started).

- Aby uzyskać ogólne informacje na temat ML Server, zobacz [wprowadzenie do języka R](https://msdn.microsoft.com/microsoft-r/microsoft-r-get-started-node) artykułu.

- Aby uzyskać informacji na temat usługi uczenie Maszynowe na HDInsight, zobacz [Przegląd usługi ML w HDInsight](r-server/r-server-overview.md) i [Rozpoczynanie pracy z usługami uczenia Maszynowego w usłudze Azure HDInsight](r-server/r-server-get-started.md).

Aby uzyskać więcej informacji przy użyciu SparkR zobacz:

- [Dokument Apache SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html).

- [Omówienie SparkR](https://docs.databricks.com/spark/latest/sparkr/overview.html) z usługi Databricks.
