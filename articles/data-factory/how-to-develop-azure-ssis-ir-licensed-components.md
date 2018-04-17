---
title: Opracowywanie płatną lub licencjonowanych składników środowiska uruchomieniowego integracji usług SSIS Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można programować niezależnego dostawcy oprogramowania, a instalacja płatnej lub licencji niestandardowych składników środowiska uruchomieniowego integracji usług SSIS Azure
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e22ca4bd5b749e8752f800590938199e06abbd34
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="develop-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Opracowywanie płatną lub licencjonowanych niestandardowych składników środowiska uruchomieniowego integracji usług SSIS Azure

## <a name="problem---the-azure-ssis-ir-requires-a-different-approach"></a>Problem - IR Azure SSIS wymaga różne podejścia

Rodzaj środowiska uruchomieniowego integracji usług SSIS Azure zawiera kilka wyzwania, które powodują, że typowe metody licencjonowania używane do instalacji lokalnej, niestandardowych składników niewystarczającego.

-   Węzły IR Azure SSIS są nietrwałe i można przydzielić lub wydane w dowolnym momencie. Na przykład można uruchomić lub zatrzymać węzłów do zarządzania kosztami lub skalować w górę i w dół za pośrednictwem różnych rozmiarach węzła. W związku z tym powiązanie licencji składników innych firm do określonego węzła przy użyciu informacji dotyczące komputera, takich jak adres MAC lub identyfikator procesora CPU nie jest już działało.

-   Można również skalować IR Azure SSIS przychodzący lub wychodzący, tak, aby liczba węzłów można powiększyć lub pomniejszyć w dowolnym momencie.

## <a name="solution---windows-environment-variables-and-ssis-system-variables-for-license-binding-and-validation"></a>Rozwiązanie - zmiennych środowiskowych systemu Windows i zmienne systemu SSIS powiązanie licencji i sprawdzania poprawności

W wyniku ograniczenia tradycyjnego licencjonowania metod opisanych w poprzedniej sekcji IR Azure SSIS udostępnia zmiennych środowiskowych systemu Windows i zmienne systemu SSIS powiązanie licencji i Weryfikacja składników innych firm. Niezależni dostawcy oprogramowania można używać tych zmiennych do uzyskiwania informacji o unikatowy i trwałe dla IR Azure SSIS, takich jak identyfikator klastra i liczba węzłów klastra. Z tych informacji niezależnym dostawcom oprogramowania można powiązać licencji dla ich składnika IR Azure SSIS *jako klaster*, o identyfikatorze, który nie ulega zmianie po klientom uruchamianie lub zatrzymywanie, skalowanie w górę lub w dół, skalowania przychodzący lub wychodzący lub ponownie skonfigurować IR Azure SSIS w dowolny sposób.

Na poniższym diagramie przedstawiono typowej instalacji, aktywacji i powiązanie licencji i sprawdzania poprawności przepływu dla składników innych firm, korzystających z tych nowych zmiennych:

![Instalacja składników licencjonowane](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instrukcje
1. Niezależni dostawcy oprogramowania mogą oferować ich licencjonowane składniki w różne jednostki magazynowe lub warstw (na przykład, jeden węzeł, maksymalnie 5 węzłów, maksymalnie 10 węzły i tak dalej). Niezależnego dostawcy oprogramowania zawiera odpowiedni klucz produktu, gdy klienci zakupu produktu. Niezależny dostawca oprogramowania można też podać kontener blob magazynu Azure, zawierający skrypt instalacyjny niezależnego dostawcy oprogramowania i skojarzone pliki. Klientów można skopiować te pliki do ich własnych kontenera magazynu oraz zmodyfikować je za pomocą własnych kluczy produktu (na przykład uruchamiając `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Klientów można następnie udostępnić lub ponownie skonfigurować IR Azure SSIS z identyfikatora URI połączenia SAS ich kontenera jako parametr. Aby uzyskać więcej informacji, zobacz [Instalacja niestandardowa środowiska uruchomieniowego integracji usług SSIS Azure](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Gdy IR Azure SSIS jest udostępnione lub ponownie skonfigurować, niezależnego dostawcy oprogramowania, Instalator zostanie uruchomiony na każdym węźle, aby zapytanie dotyczące zmiennych środowiskowych systemu Windows, `SSIS_CLUSTERID` i `SSIS_CLUSTERNODECOUNT`. Następnie IR Azure SSIS przesyła jego identyfikator klastra i klucz produktu licencjonowanego produktu do serwera aktywacji niezależnego dostawcy oprogramowania do wygenerowania klucza aktywacji.

3. Po otrzymaniu klucza aktywacji, Instalator niezależnego dostawcy oprogramowania można przechowywać klucz lokalnie na każdym węźle (na przykład w rejestrze).

4. Uruchomienie pakietu, który używa niezależnego dostawcy oprogramowania licencjonowanego składnika w węźle IR Azure SSIS, klienci pakietu odczytuje przechowywane lokalnie klucz aktywacji, a następnie zweryfikuje go przed identyfikatorem węzła klastra. Pakiet można również opcjonalnie raportów liczba węzłów klastra do serwera aktywacji niezależnego dostawcy oprogramowania.

    Oto przykład kodu, który sprawdza poprawność klucza aktywacji i raporty liczba węzłów klastra:

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

## <a name="next-steps"></a>Kolejne kroki

-   [Instalacja niestandardowa środowiska uruchomieniowego integracji usług SSIS Azure](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Środowisko uruchomieniowe integracji usług SSIS Azure w wersji Enterprise](how-to-configure-azure-ssis-ir-enterprise-edition.md)