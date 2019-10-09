---
title: Tworzenie pierwszej aplikacji usługi Azure Service Fabric w systemie Linux C#przy użyciu programu | Microsoft Docs
description: Dowiedz się, jak utworzyć i wdrożyć aplikację Service Fabric C# przy użyciu programu i platformy .net Core 2,0.
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: atsenthi
ms.openlocfilehash: 8c34f50e8eb9d9793c146c38aca47691b04049c0
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167303"
---
# <a name="create-your-first-azure-service-fabric-application"></a>Tworzenie pierwszej aplikacji usługi Azure Service Fabric
> [!div class="op_single_selector"]
> * [Java — Linux (wersja zapoznawcza)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C#— Linux (wersja zapoznawcza)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric udostępnia zestawy SDK do kompilowania usług w systemie Linux w oprogramowaniu .NET Core i Java. W tym samouczku przedstawiono sposób tworzenia aplikacji dla systemu Linux i tworzenia usługi przy użyciu C# platformy .net Core 2,0.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że [skonfigurowano środowisko deweloperskie systemu Linux](service-fabric-get-started-linux.md). W przypadku korzystania z Mac OS X można [skonfigurować środowisko systemu Linux jednokrotne na maszynie wirtualnej przy użyciu Vagrant](service-fabric-get-started-mac.md).

Trzeba również zainstalować [interfejs wiersza polecenia Service Fabric](service-fabric-cli.md)

### <a name="install-and-set-up-the-generators-for-c"></a>Zainstaluj i skonfiguruj generatory dlaC#
Service Fabric udostępnia narzędzia do tworzenia szkieletu, które ułatwiają tworzenie Service Fabric aplikacji z poziomu terminalu przy użyciu generatorów szablonów narzędzia Yeoman. Wykonaj następujące kroki, aby skonfigurować generatory szablonów narzędzia Yeoman Service Fabric dla C#:

1. Zainstaluj na komputerze NodeJS i NPM

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. Zainstaluj Generator szablonów [Narzędzia Yeoman](https://yeoman.io/) na maszynie z poziomu usługi npm

   ```bash
   npm install -g yo
   ```
3. Zainstaluj Generator aplikacji Service Fabric C# narzędzia YEOMAN z npm

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>Tworzenie aplikacji
Aplikacja Service Fabric może zawierać jedną lub więcej usług, z których każda ma określoną rolę w dostarczaniu funkcjonalności aplikacji. Service Fabric Generator [Narzędzia Yeoman](https://yeoman.io/) dla C#, który został zainstalowany w ostatnim kroku, ułatwia utworzenie pierwszej usługi i dodanie jej później. Użyjmy narzędzia Yeoman do utworzenia aplikacji z jedną usługą.

1. W terminalu wpisz następujące polecenie, aby rozpocząć tworzenie szkieletu: `yo azuresfcsharp`
2. Nadaj nazwę aplikacji.
3. Wybierz typ pierwszej usługi i nadaj jej nazwę. Na potrzeby tego samouczka wybieramy niezawodną usługę aktora.

   ![Service Fabric Generator narzędzia Yeoman dlaC#][sf-yeoman]

> [!NOTE]
> Aby uzyskać więcej informacji na temat tych opcji, zobacz [Service Fabric model programowania — Omówienie](service-fabric-choose-framework.md).
>
>

## <a name="build-the-application"></a>Kompilowanie aplikacji
Service Fabric szablony narzędzia Yeoman obejmują skrypt kompilacji, którego można użyć do kompilowania aplikacji z terminalu (po przejściu do folderu aplikacji).

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>Wdrażanie aplikacji

Po skompilowaniu aplikacji można wdrożyć ją w klastrze lokalnym.

1. Nawiązywanie połączenia z lokalnym klastrem Service Fabric.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Uruchom skrypt instalacji podany w szablonie, aby skopiować pakiet aplikacji do magazynu obrazów klastra, zarejestrować typ aplikacji i utworzyć wystąpienie aplikacji.

    ```bash
    ./install.sh
    ```

Wdrożenie skompilowanej aplikacji jest takie samo jak w przypadku innych aplikacji Service Fabric. Szczegółowe instrukcje można znaleźć w dokumentacji dotyczącej [zarządzania aplikacją Service Fabric przy użyciu interfejsu wiersza polecenia Service Fabric](service-fabric-application-lifecycle-sfctl.md) .

Parametry tych poleceń można znaleźć w wygenerowanych manifestach wewnątrz pakietu aplikacji.

Po wdrożeniu aplikacji otwórz przeglądarkę i przejdź do [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) w [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Następnie rozwiń węzeł **aplikacje** i zwróć uwagę, że istnieje teraz wpis dla typu aplikacji i drugi dla pierwszego wystąpienia tego typu.

> [!IMPORTANT]
> Aby wdrożyć aplikację do bezpiecznego klastra z systemem Linux na platformie Azure, musisz skonfigurować certyfikat w celu weryfikacji aplikacji przy użyciu środowiska uruchomieniowego Service Fabric. Dzięki temu usługi Reliable Services mogą komunikować się z podstawowymi interfejsami API środowiska uruchomieniowego Service Fabric. Aby dowiedzieć się więcej, zobacz [Konfigurowanie aplikacji Reliable Services do uruchamiania w klastrach systemu Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Uruchom klienta testowego i przeprowadź przejście w tryb failover
Projekty aktora nie wykonują żadnych czynności samodzielnie. Wymagają one innej usługi lub klienta do wysyłania wiadomości. Szablon aktora zawiera prosty skrypt testowy, którego można użyć do współpracy z usługą aktora.

1. Uruchom skrypt za pomocą narzędzia Watch, aby wyświetlić dane wyjściowe usługi aktora.

   W przypadku systemu MAC OS X należy skopiować folder myactorsvcTestClient do tej samej lokalizacji wewnątrz kontenera, uruchamiając następujące polecenia dodatkowe.
    
    ```bash
    docker cp  [first-four-digits-of-container-ID]:/home
    docker exec -it [first-four-digits-of-container-ID] /bin/bash
    cd /home
    ```
    
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. W Service Fabric Explorer zlokalizuj węzeł obsługujący replikę podstawową dla usługi aktora. Na poniższym zrzucie ekranu jest to węzeł 3.

    ![Znajdowanie repliki podstawowej w Service Fabric Explorer][sfx-primary]
3. Kliknij węzeł znaleziony w poprzednim kroku, a następnie wybierz pozycję **Dezaktywuj (Uruchom ponownie)** z menu Akcje. Ta akcja powoduje ponowne uruchomienie jednego węzła w klastrze lokalnym w celu wymuszenia przejścia w tryb failover do repliki pomocniczej uruchomionej w innym węźle. Podczas wykonywania tej akcji należy zwrócić uwagę na dane wyjściowe klienta testowego i zwrócić uwagę, że licznik nadal rośnie poza trybem failover.

## <a name="adding-more-services-to-an-existing-application"></a>Dodawanie kolejnych usług do istniejącej aplikacji

Aby dodać kolejną usługę do aplikacji już utworzonej przy użyciu `yo`, wykonaj następujące czynności:
1. Zmień katalog na katalog główny istniejącej aplikacji.  Na przykład `cd ~/YeomanSamples/MyApplication`, jeśli `MyApplication` jest aplikacją utworzoną przez narzędzia Yeoman.
2. Uruchom `yo azuresfcsharp:AddService`

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z klastrów Service Fabric przy użyciu interfejsu wiersza polecenia Service Fabric](service-fabric-cli.md)
* Dowiedz się więcej o [opcjach pomocy technicznej Service Fabric](service-fabric-support.md)
* [Wprowadzenie do korzystania z interfejsu wiersza polecenia Service Fabric](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
