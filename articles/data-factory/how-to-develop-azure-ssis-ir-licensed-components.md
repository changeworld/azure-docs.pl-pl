---
title: Zainstaluj licencjonowanych składników dla środowiska Azure-SSIS integration runtime | Dokumentacja firmy Microsoft
description: Dowiedz się, jak opracować niezależnym dostawcą oprogramowania i instalacji wersji płatnej lub licencjonowane składniki niestandardowe środowiska Azure-SSIS integration Runtime
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 80d4fff03422beacccd3aff3cdd8cb1047d5f5af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61344660"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Instalowanie wersji płatnej lub licencjonowane składniki niestandardowe środowiska Azure-SSIS integration Runtime

W tym artykule opisano, jak niezależnego dostawcy oprogramowania mogą tworzyć i zainstalować płatną lub licencjonowanych składników niestandardowych pakietów usług SQL Server Integration Services (SSIS), które działają na platformie Azure w środowisko Azure-SSIS integration runtime.

## <a name="the-problem"></a>Problem

Rodzaj środowiska Azure-SSIS integration runtime przedstawiono kilka kwestii, które powodują, że typowe metody licencjonowania, używane do instalacji lokalnych składników niestandardowych niewystarczające. W rezultacie Azure-SSIS IR wymaga innego podejścia.

-   Węzły środowiska Azure-SSIS IR są nietrwałe i mogą być przydzielane lub udostępnić w dowolnym momencie. Na przykład możesz rozpocząć lub zatrzymać węzłów do zarządzania kosztami, lub Skaluj w górę i w dół za pośrednictwem różnych rozmiarów węzła. W rezultacie powiązanie licencji składników innych firm do określonego węzła przy użyciu informacji specyficznych dla maszyny, takich jak adres MAC lub identyfikator procesora CPU nie jest już dostępny.

-   Możesz również skalować Azure-SSIS IR wewnątrz lub na zewnątrz, tak aby liczba węzłów można powiększyć lub pomniejszyć w dowolnym momencie.

## <a name="the-solution"></a>Rozwiązanie

W wyniku ograniczenia tradycyjnego licencjonowania metod opisanych w poprzedniej sekcji Azure-SSIS IR zawiera nowe rozwiązanie. To rozwiązanie używa zmiennych środowiskowych Windows i zmienne systemowe SSIS powiązania licencji i weryfikacji składników innych firm. Niezależni dostawcy oprogramowania, można użyć tych zmiennych do uzyskiwania informacji o unikatowy i trwałe dla środowiska IR Azure-SSIS, takie jak identyfikator klastra i liczby węzłów klastra. Za pomocą tych informacji niezależnych dostawców oprogramowania można następnie powiązać licencji dla ich składnika środowiska Azure-SSIS IR *jako klaster*. Identyfikator, który nie powoduje zmiany klientów uruchomić lub zatrzymać, skalowanie w górę lub w dół skalowania wewnątrz lub na zewnątrz lub ponownie skonfigurować środowisko IR Azure-SSIS w jakikolwiek sposób używa tego powiązania.

Na poniższym diagramie przedstawiono typowej instalacji i aktywacji licencji powiązania, a Weryfikacja przepływu dla składników innych firm, które są używane następujące nowe zmienne:

![Instalacja licencjonowanych składników](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instrukcje
1. Niezależni dostawcy oprogramowania mogą zaoferować łączność swoich licencjonowanych składników w różnych jednostek SKU lub warstwy (na przykład jeden węzeł, maksymalnie 5 węzłów, maksymalnie 10 węzłów i tak dalej). Niezależny dostawca oprogramowania zapewnia odpowiedniego klucza produktu, gdy klienci zakupu produktu. Niezależny dostawca oprogramowania mogą także podać kontener obiektów blob usługi Azure Storage, który zawiera skrypt instalacji niezależnego dostawcy oprogramowania i skojarzone pliki. Klientów można skopiować te pliki do ich własnych kontener magazynu i zmodyfikuj je za pomocą własnych kluczy produktu (na przykład uruchamiając `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Klientów można następnie aprowizacji lub ponownie skonfigurować środowisko IR Azure-SSIS za pomocą identyfikatora URI sygnatury dostępu Współdzielonego kontenera, ich jako parametr. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Gdy środowisko IR Azure-SSIS jest aprowizowane lub ponownie skonfigurowane, niezależnego dostawcy oprogramowania, Instalator zostanie uruchomiony w każdym węźle, aby wysłać zapytanie do zmiennych środowiskowych Windows `SSIS_CLUSTERID` i `SSIS_CLUSTERNODECOUNT`. Następnie Azure-SSIS IR przesyła jego identyfikator klastra i klucz produktu dla licencjonowanego produktu do serwera aktywacji niezależnych dostawców oprogramowania, aby wygenerować klucz aktywacji.

3. Po otrzymaniu klucza aktywacji, Instalator niezależnego dostawcy oprogramowania mogą przechowywania klucza lokalnie w każdym węźle (na przykład rejestr).

4. Po uruchomieniu pakietu, który jest składnikiem niezależnym dostawcą oprogramowania licencjonowanego w węźle środowiska Azure-SSIS IR klientów pakietu odczytuje klucz aktywacji przechowywane lokalnie, a następnie zweryfikuje go względem identyfikatora węzła klastra. Pakiet również opcjonalnie może zgłaszać liczby węzłów klastra na serwerze aktywacji niezależnego dostawcy oprogramowania.

    Poniżej przedstawiono przykładowy kod, który sprawdza poprawność klucza aktywacji i raporty liczby węzłów klastra:

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

## <a name="isv-partners"></a>Partnerskim niezależnym dostawcom oprogramowania

Można znaleźć listy partnerów niezależnych dostawców oprogramowania, którzy mają dostosowane ich części i rozszerzenia dla Azure-SSIS IR na końcu tego wpisu w blogu - [Enterprise Edition, ustawienia niestandardowe i 3 rozszerzeń innych firm dla usług SSIS w usłudze ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/).

## <a name="next-steps"></a>Kolejne kroki

-   [Niestandardowa konfiguracja środowiska Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition, środowiska Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
