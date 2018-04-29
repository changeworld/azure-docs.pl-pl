---
title: Obliczeniowych aplikacji Java na maszynie Wirtualnej
description: Dowiedz się, jak utworzyć maszynę wirtualną platformy Azure, która uruchomi aplikację Java obliczeniowych, które mogą być monitorowane przez inną aplikację Java.
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: mbaldwin
tags: azure-service-management,azure-resource-manager
ms.assetid: ae6f2737-94c7-4569-9913-d871450c2827
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: e8da296c30f1d2be3c637e456e90d2f93da67548
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Jak uruchomić zadanie wymagające wielu obliczeń w środowisku Java na maszynie wirtualnej
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Przy użyciu platformy Azure maszyny wirtualnej służy do obsługi zadań obliczeniowych. Na przykład maszynę wirtualną można obsłużyć zadania i dostarczania wyniki do komputerów klienckich lub aplikacji dla urządzeń przenośnych. Po przeczytaniu tego artykułu, trzeba będzie zrozumienia sposobu tworzenia maszyny wirtualnej, która uruchamia aplikację Java obliczeniowych, które mogą być monitorowane przez inną aplikację Java.

Ten samouczek zakłada informacji o sposobie tworzenia aplikacji konsoli Java, można zaimportować biblioteki do aplikacji w języku Java i może powodować generowanie archiwum Java (JAR). Przyjęto, że nie wie, Microsoft Azure.

Dowiesz się:

* Jak utworzyć maszynę wirtualną z Java Development Kit (JDK) już zainstalowane.
* Jak zdalne logowanie do maszyny wirtualnej.
* Jak utworzyć przestrzeń nazw magistrali usług.
* Jak utworzyć aplikację Java, która wykonuje zadanie obliczeniowych.
* Jak utworzyć aplikację Java, która monitoruje postęp zadania obliczeniowych.
* Jak uruchomić aplikacji Java.
* Jak można zatrzymać aplikacji Java.

W tym samouczku będzie używać Problem sprzedawcy Traveling zadań obliczeniowych. Oto przykład aplikacji Java uruchamianie zadania obliczeniowych.

![Solver sprzedawcy Traveling Problem][solver_output]

Oto przykład monitorowania zadań obliczeniowych aplikacji Java.

![Problem sprzedawcy Traveling klienta][client_output]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Aby utworzyć maszynę wirtualną
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **Utwórz zasób**, kliknij przycisk **obliczeniowe**, kliknij przycisk **maszyny wirtualnej**, a następnie kliknij przycisk **z galerii**.
3. W **wybierz obraz maszyny wirtualnej** okno dialogowe, wybierz opcję **JDK 7 systemu Windows Server 2012**.
   Należy pamiętać, że **JDK 6 systemu Windows Server 2012** jest dostępna w przypadku, gdy masz starsze aplikacje, które nie są jeszcze gotowy do uruchomienia w JDK 7.
4. Kliknij przycisk **Dalej**.
5. W **konfiguracji maszyny wirtualnej** okno dialogowe:
   1. Określ nazwę maszyny wirtualnej.
   2. Określ rozmiar czcionki do użycia dla maszyny wirtualnej.
   3. Wprowadź nazwę administratora **nazwy użytkownika** pola. Należy pamiętać, ta nazwa i hasło, które można wprowadzić obok, użyjesz ich podczas logowania zdalnego do maszyny wirtualnej.
   4. Wprowadź hasło w **nowe hasło** pól i wprowadź go ponownie **Potwierdź** pola. Jest to hasło konta administratora.
   5. Kliknij przycisk **Dalej**.
6. W następnej **konfiguracji maszyny wirtualnej** okno dialogowe:
   1. Dla **usługi w chmurze**, użyj domyślnej **Utwórz nową usługę w chmurze**.
   2. Wartość **nazwa DNS usługi w chmurze** musi być unikatowa w cloudapp.net. W razie potrzeby zmodyfikuj tę wartość, tym Azure oznacza jest unikatowa.
   3. Określ region, grupę koligacji lub sieci wirtualnej. Do celów tego samouczka, określ region, takich jak **zachodnie stany USA**.
   4. Dla **konta magazynu**, wybierz pozycję **użyć konta magazynu automatycznie generowanych**.
   5. Aby uzyskać **zestawu dostępności**, wybierz pozycję **(Brak)**.
   6. Kliknij przycisk **Dalej**.
7. W ostatecznym **konfiguracji maszyny wirtualnej** okno dialogowe:
   1. Zaakceptuj domyślną wpisy punktów końcowych.
   2. Kliknij przycisk **Complete** (Zakończ).

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Aby zdalnie zalogować się do maszyny wirtualnej
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **maszyn wirtualnych**.
3. Kliknij nazwę maszyny wirtualnej, która ma logować się do.
4. Kliknij przycisk **Połącz**.
5. Odpowiedz na monity w razie potrzeby można połączyć z maszyną wirtualną. Po wyświetleniu monitu o nazwę administratora i hasła, należy użyć wartości podanych podczas tworzenia maszyny wirtualnej.

Należy pamiętać, że funkcje usługi Azure Service Bus wymaga certyfikat główny firmy CyberTrust Baltimore instalowanego jako część Twojego środowiska JRE **cacerts** przechowywania. Ten certyfikat jest automatycznie uwzględniany w języku Java Runtime Environment (JRE) używane w tym samouczku. Jeśli nie masz ten certyfikat w Twojej środowiska JRE **cacerts** przechowywania, zobacz [Dodawanie certyfikatu do magazynu certyfikatów urzędu certyfikacji Java] [ add_ca_cert] informacji na temat dodawania go (a także informacje o wyświetlaniu certyfikaty w magazynie cacerts).

## <a name="how-to-create-a-service-bus-namespace"></a>Jak utworzyć przestrzeń nazw magistrali usług
Aby rozpocząć korzystanie z kolejek usługi Service Bus na platformie Azure, należy najpierw utworzyć przestrzeń nazw usługi. Przestrzeń nazw usługi zapewnia kontener zakresu na potrzeby adresowania zasobów usługi Service Bus w aplikacji.

Aby utworzyć przestrzeń nazw usługi:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W okienku nawigacji w lewym dolnym w portalu Azure kliknij **usługi Service Bus, kontroli dostępu i buforowanie**.
3. W okienku lewej górnej części portalu Azure kliknij **usługi Service Bus** węzeł, a następnie kliknij przycisk **nowy** przycisku.  
   ![Zrzut ekranu węzeł magistrali usług][svc_bus_node]
4. W **tworzenie nowych Namespace usługi** okna dialogowego wprowadź **Namespace**, a następnie upewnij się, że jest unikatowa, kliknij **Sprawdź dostępność** przycisku.  
   ![Utwórz nowy Namespace zrzut ekranu][create_namespace]
5. Po upewnieniu się, nazwę przestrzeni nazw jest dostępna, wybierz kraj lub region, w którym ma być hostowana przestrzeni nazw, a następnie kliknij **utworzyć Namespace** przycisku.  
   
   Przestrzeń nazw, utworzony zostanie następnie wyświetlona w portalu Azure i zajmuje kilka minut, aby aktywować. Poczekaj, aż stan **Active** przed przejściem do następnego kroku.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Uzyskiwanie poświadczeń zarządzania domyślnej przestrzeni nazw
W celu wykonywania operacji zarządzania, takich jak tworzenie kolejki w nowej przestrzeni nazw, należy uzyskać poświadczenia zarządzania dla przestrzeni nazw.

1. W okienku nawigacji po lewej stronie kliknij **usługi Service Bus** węzeł, aby wyświetlić listę dostępnych przestrzeni nazw.
   ![Dostępne obszary nazw zrzut ekranu][avail_namespaces]
2. Wybierz nowo utworzoną na wyświetlonej liście przestrzeni nazw.
   ![Zrzut ekranu listy Namespace][namespace_list]
3. Po prawej stronie **właściwości** okienko jest wyświetlana lista właściwości nowej przestrzeni nazw.
   ![Zrzut ekranu okienka właściwości][properties_pane]
4. **Domyślny klucz** jest ukryty. Kliknij przycisk **widoku** przycisk, aby wyświetlić poświadczeń zabezpieczeń.
   ![Zrzut ekranu klucz domyślny][default_key]
5. Zanotuj **domyślne wystawcy** i **domyślny klucz** jako użyje tych informacji poniżej w celu wykonania operacji z przestrzenią nazw.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Tworzenie aplikacji Java, która wykonuje zadanie obliczeniowych
1. Na komputerze deweloperskim (który nie ma być maszynę wirtualną, która została utworzona) Pobierz [zestawu Azure SDK dla języka Java](https://azure.microsoft.com/develop/java/).
2. Utwórz aplikację konsoli języka Java przy użyciu przykładowy kod na końcu tej sekcji. W tym samouczku użyjemy **TSPSolver.java** jako nazwa pliku języka Java. Modyfikowanie **Twojego\_usługi\_magistrali\_przestrzeni nazw**, **Twojego\_usługi\_magistrali\_właściciela**i **Twojego\_usługi\_magistrali\_klucza** symbole zastępcze do użycia z usługi service bus **przestrzeni nazw**, **domyślne wystawcy** i  **Domyślny klucz** wartości odpowiednio.
3. Po kodowania, wyeksportować aplikację do uruchomienia archiwum Java (JAR) i tworzenia pakietów w wygenerowanym JAR wymaganych bibliotek. W tym samouczku użyjemy **TSPSolver.jar** jako nazwa wygenerowana JAR.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Tworzenie aplikacji Java, która monitoruje postęp zadania obliczeniowych
1. Na komputerze deweloperskim Utwórz aplikację konsoli języka Java przy użyciu przykładowy kod na końcu tej sekcji. W tym samouczku użyjemy **TSPClient.java** jako nazwa pliku języka Java. Jak pokazano wcześniej, zmodyfikuj **Twojego\_usługi\_magistrali\_przestrzeni nazw**, **Twojego\_usługi\_magistrali\_właściciela**, i **Twojego\_usługi\_magistrali\_klucza** symbole zastępcze do użycia z usługi service bus **przestrzeni nazw**, **domyślne wystawcy**i **domyślny klucz** wartości odpowiednio.
2. Eksportowanie aplikacji do JAR do uruchomienia i pakietów wymaganych bibliotek w wygenerowanym JAR. W tym samouczku użyjemy **TSPClient.jar** jako nazwa wygenerowana JAR.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Jak uruchomić aplikacji Java
Uruchom aplikację obliczeniowych, aby najpierw utworzyć kolejkę, następnie w rozwiązaniu problemu Saleseman podróży, który doda bieżącego najlepszą trasę do kolejki magistrali usług. Podczas gdy aplikacja obliczeniowych jest uruchomione (lub później), uruchom klienta, aby wyświetlić wyniki z kolejką usługi service bus.

### <a name="to-run-the-compute-intensive-application"></a>Aby uruchomić aplikację obliczeniowych
1. Zaloguj się do maszyny wirtualnej.
2. Utwórz folder, w którym będzie działała aplikacja sieci. Na przykład **c:\TSP**.
3. Kopiuj **TSPSolver.jar** do **c:\TSP**,
4. Utwórz plik o nazwie **c:\TSP\cities.txt** z następującą zawartość.
   
        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33
5. W wierszu polecenia Zmień katalog na c:\TSP.
6. Upewnij się, że folder bin środowiska JRE znajduje się w zmiennej środowiskowej PATH.
7. Należy utworzyć kolejką usługi service bus przed uruchomieniem permutacji solver TSP. Uruchom następujące polecenie, aby utworzyć kolejką usługi service bus.
   
        java -jar TSPSolver.jar createqueue
8. Teraz, gdy trwa tworzenie kolejki, możesz uruchomić permutacji solver TSP. Na przykład uruchom następujące polecenie, aby uruchomić solver dla 8 miast.
   
        java -jar TSPSolver.jar 8
   
   Jeśli nie określisz numeru, zostanie on uruchomiony dla 10 miast. Jak znalezieniem bieżącej trasy najkrótszy spowoduje to dodanie ich do kolejki.

> [!NOTE]
> Im większa liczba, która zostanie określona, tym dłużej solver zostanie uruchomiony. Na przykład uruchomiony 14 miasta może potrwać kilka minut i uruchamiane przez 15 miasta może zająć kilka godzin. Zwiększenie do więcej niż 16 miast może spowodować dni środowiska uruchomieniowego (ostatecznie tygodnie, miesiące i lata). Jest to spowodowane szybki wzrost liczbę permutacji obliczone przez moduł rozwiązywania jako liczba wzrasta miast.
> 
> 

### <a name="how-to-run-the-monitoring-client-application"></a>Jak uruchomić monitorowania aplikacji klienta
1. Zaloguj się na komputerze, na którym należy uruchomić aplikacji klienckiej. Musi to być tym samym komputerze z systemem **TSPSolver** aplikacji, mimo że można go.
2. Utwórz folder, w którym będzie działała aplikacja sieci. Na przykład **c:\TSP**.
3. Kopiuj **TSPClient.jar** do **c:\TSP**,
4. Upewnij się, że folder bin środowiska JRE znajduje się w zmiennej środowiskowej PATH.
5. W wierszu polecenia Zmień katalog na c:\TSP.
6. Uruchom następujące polecenie.
   
        java -jar TSPClient.jar
   
    Opcjonalnie określ liczbę minut w tryb uśpienia Between sprawdzanie kolejki, przekazując argument wiersza polecenia. Domyślny okres uśpienia sprawdzania kolejki wynosi 3 minuty, który jest używany, jeśli żaden argument wiersza polecenia jest przekazywana do **TSPClient**. Jeśli chcesz użyć innej wartości interwału uśpienia, na przykład jedną minutę, uruchom następujące polecenie.
   
        java -jar TSPClient.jar 1
   
    Klient zostanie uruchomiony, dopóki nie widzi ona komunikatu w kolejce "Zakończ". Należy pamiętać, że po uruchomieniu wielu wystąpień solver bez uruchamiania klienta może być konieczne z klientem wiele razy, aby całkowicie opróżnić kolejkę. Alternatywnie możesz usunąć kolejkę i ponownie go utworzyć. Aby usunąć kolejkę, uruchom następujące polecenie **TSPSolver** (nie **TSPClient**) polecenia.
   
        java -jar TSPSolver.jar deletequeue
   
    Solver zostanie uruchomiony do momentu zakończenia badanie wszystkie trasy.

## <a name="how-to-stop-the-java-applications"></a>Zatrzymywanie aplikacji Java
Zarówno dla zmiennej i aplikacje klienckie, można nacisnąć klawisz **klawisze Ctrl + C** aby zakończyć, jeśli chcesz zakończyć przed ukończeniem normalnego.

[solver_output]:media/java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]:media/java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]:media/java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]:media/java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]:media/java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]:media/java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]:media/java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]:media/java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../../../java-add-certificate-ca-store.md
