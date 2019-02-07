---
title: Łącznik Eksplorator danych usługi Azure dla platformy Apache Spark
description: W tym artykule przedstawiono sposób korzystania z łącznika Eksplorator danych platformy Azure dla platformy Apache Spark.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 6f115df97d0b790c94d6dc07f3f40feeceb1a7cc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824213"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Łącznik Eksplorator danych usługi Azure dla platformy Apache Spark

Za pomocą Eksploratora danych platformy Azure i Apache Spark, można tworzyć szybkich i skalowalnych aplikacji, przeznaczone dla scenariuszy, takich jak machine learning (ML), wyodrębnianie-transformacja-ładowanie (ETL) i usługi Log Analytics w oparciu o dane.

## <a name="prerequisites"></a>Wymagania wstępne

Do korzystania z łącznika, aplikacja musi mieć:

* Java 1.8 SDK
* Narzędzie maven 3.x
* Platforma Spark w wersji 2.3.2 lub nowszej

## <a name="link-to-data-explorer"></a>Link do Eksploratora danych

W przypadku aplikacji z języka Scala i Java przy użyciu narzędzia Maven project definicje, Połącz swoją aplikację przy użyciu następującego artefaktu:

```java
groupId = com.microsoft.azure
artifactId = spark-kusto-connector
version = 1.0.0-Beta-01 
```

W narzędziu Maven należy połączyć się z następujących czynności:

```Maven
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-kusto-connector</artifactId>
    <version>1.0.0-Beta-01</version>
  </dependency>
```

## <a name="build-commands"></a>Tworzenie poleceń

Aby skompilować plik jar i uruchomić wszystkie testy:

```
mvn clean package
```

Aby utworzyć plik jar, uruchamianie wszystkich testów i zainstaluj plik jar do lokalnego repozytorium Maven:

```
mvn clean install
```