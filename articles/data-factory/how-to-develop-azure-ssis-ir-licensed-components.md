---
title: Zainstaluj licencjonowane składniki dla środowiska Azure-SSIS Integration Runtime | Microsoft Docs
description: Dowiedz się, w jaki sposób dostawca ISV może opracowywać i instalować płatne lub licencjonowane składniki niestandardowe dla środowiska Azure-SSIS Integration Runtime
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 1c574578e6ed6ee032be01718eb3e8afd27fdf6f
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708008"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Zainstaluj płatne lub licencjonowane składniki niestandardowe dla środowiska Azure-SSIS Integration Runtime

W tym artykule opisano, jak niezależnego dostawcy oprogramowania mogą opracowywać i instalować płatne lub licencjonowane składniki niestandardowe dla pakietów SQL Server Integration Services (SSIS) działających na platformie Azure w środowisku Azure-SSIS Integration Runtime.

## <a name="the-problem"></a>Problem

Charakter środowiska Azure-SSIS Integration Runtime zawiera kilka wyzwań, które sprawiają, że typowe metody licencjonowania używane na potrzeby instalacji lokalnych składników niestandardowych są nieodpowiednie. W związku z tym środowisko Azure-SSIS IR wymaga innego podejścia.

-   Węzły środowiska Azure-SSIS IR są nietrwałe i mogą być przydzielona lub wydawane w dowolnym momencie. Można na przykład uruchomić lub zatrzymać węzły, aby zarządzać kosztami, lub skalować w górę i w dół za pomocą różnych rozmiarów węzłów. W związku z tym powiązanie licencji składnika innej firmy z określonym węzłem przy użyciu informacji specyficznych dla komputera, takich jak adres MAC lub identyfikator procesora, nie jest już możliwe.

-   Możesz również skalować środowisko Azure-SSIS IR w systemie i w dowolnym momencie, aby liczba węzłów mogła się zmniejszyć lub zwiększyć.

## <a name="the-solution"></a>Rozwiązanie

W wyniku ograniczeń tradycyjnych metod licencjonowania opisanych w poprzedniej sekcji środowisko Azure-SSIS IR udostępnia nowe rozwiązanie. To rozwiązanie używa zmiennych środowiskowych systemu Windows i zmiennych systemowych SSIS do powiązania licencji i weryfikacji składników innych firm. Dostawcy ISV mogą używać tych zmiennych do uzyskiwania unikatowych i trwałych informacji dla środowiska Azure-SSIS IR, takich jak identyfikator klastra i liczba węzłów klastra. Korzystając z tych informacji, dostawcy ISV mogą następnie powiązać licencję składnika z usługą Azure-SSIS IR *jako klaster*. To powiązanie używa identyfikatora, który nie zmienia się, gdy klienci zaczynają lub zatrzymują, skalować w górę lub w dół, skalować w górę lub w dół, lub ponownie skonfigurować środowisko Azure-SSIS IR w dowolny sposób.

Na poniższym diagramie przedstawiono typowe powiązania instalacji, aktywacji i licencji oraz przepływy walidacji dla składników innych firm korzystających z tych nowych zmiennych:

![Instalacja licencjonowanych składników](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instrukcje
1. Niezależni dostawcy oprogramowania mogą oferować licencjonowane składniki w różnych jednostkach SKU i warstwach (na przykład pojedynczy węzeł, maksymalnie 5 węzłów, do 10 węzłów itd.). Dostawca oprogramowania dostarcza odpowiedni klucz produktu, gdy klienci kupują produkt. Dostawca niezależnego dostawcy oprogramowania może również udostępnić kontener obiektów BLOB usługi Azure Storage, który zawiera skrypt instalacji niezależnego dostawcy oprogramowania i skojarzone pliki. Klienci mogą kopiować te pliki do własnego kontenera magazynu i modyfikować je przy użyciu własnego klucza produktu (na przykład przez uruchomienie `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Klienci mogą następnie inicjować lub ponownie konfigurować środowisko Azure-SSIS IR przy użyciu identyfikatora URI sygnatury dostępu współdzielonego kontenera jako parametru. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Po zainicjowaniu lub ponownym skonfigurowaniu środowiska Azure-SSIS IR Instalator ISV działa w każdym węźle, `SSIS_CLUSTERID` aby wykonać zapytanie o zmienne środowiskowe systemu Windows i. `SSIS_CLUSTERNODECOUNT` Następnie środowisko Azure-SSIS IR przesyła identyfikator klastra i klucz produktu licencjonowanego produktu do serwera aktywacji niezależnego dostawcy oprogramowania w celu wygenerowania klucza aktywacji.

3. Po otrzymaniu klucza aktywacji program instalacyjny ISV może przechowywać klucz lokalnie w każdym węźle (na przykład w rejestrze).

4. Gdy klienci uruchamiają pakiet, który używa licencjonowanego składnika niezależnego dostawcy oprogramowania w węźle środowiska Azure-SSIS IR, pakiet odczytuje lokalnie przechowywany klucz aktywacji i weryfikuje go względem identyfikatora klastra węzła. Pakiet może również opcjonalnie zgłosić liczbę węzłów klastra do serwera aktywacji niezależnego dostawcy oprogramowania.

    Oto przykład kodu, który sprawdza poprawność klucza aktywacji i zgłasza liczbę węzłów klastra:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>Partnerzy niezależnego dostawcy oprogramowania

Listę partnerów niezależnych dostawców oprogramowania, którzy dostosowali składniki i rozszerzenia dla środowiska Azure-SSIS IR, można znaleźć na końcu tego wpisu w blogu — [Enterprise Edition, konfiguracji niestandardowej i rozszerzalności innych firm dla usług SSIS w podajniku ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>Następne kroki

-   [Konfiguracja niestandardowa środowiska Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition Integration Runtime platformy Azure — SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
