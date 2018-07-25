---
title: Informacje o wersji zestawu Azure SDK dla platformy .NET 2.9
description: Informacje o wersji zestawu Azure SDK dla platformy .NET 2.9
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 01b8ccc9fe6b5469408131bce68a903f15382998
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222532"
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Informacje o wersji zestawu Azure SDK dla platformy .NET 2.9

Ten temat zawiera informacje o wersji programu w wersji 2.9 i 2.9.6 zestawu Azure SDK dla platformy .NET.

## <a name="azure-sdk-for-net-296-release-summary"></a>Zestaw Azure SDK dla platformy .NET 2.9.6 release summary

Data wydania: 11/16/2016
 
W tej wersji zostały wprowadzone nie zmiany Azure SDK 2.9. Istnieje również nie procesu uaktualniania wymagane korzystanie z tego zestawu SDK przy użyciu istniejących projektów usług w chmurze.

### <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 w wersji Release Candidate

- W programie Visual Studio 2017 RC tej wersji zestawu Azure SDK dla platformy .NET jest wbudowana w przypadku obciążeń platformy Azure. Wszystkie narzędzia, które należy wykonać programowanie na platformie Azure będzie częścią Visual Studio 2017 RC w przyszłości. Dla programu Visual Studio 2015 i Visual Studio 2013 SDK będą nadal dostępne za pośrednictwem Instalatora WebPI. Firma Microsoft będzie można Rezygnacja z zestawu Azure SDK dla wersji platformy .NET dla programu Visual Studio 2013, gdy program Visual Studio 2017 zwolni jako produktu końcowego. Kliknij ten link, aby pobrać program Visual Studio 2017 RC: https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Diagnostyka Azure

- Zmienić zachowanie, aby przechowywać parametry połączenia częściowe tylko przy użyciu klucza zastępuje token dla parametrów połączenia magazynu diagnostyki usługi w chmurze. Klucz rzeczywisty magazyn jest teraz przechowywany w folderze profilu użytkownika, sterować jego dostępem. Program Visual Studio odczyta klucza magazynu z folderu profilu użytkownika dla debugowania lokalnego i proces publikowania. 
- W odpowiedzi na zmiany opisane powyżej, zespołu programu Visual Studio Online rozszerzone szablon zadania wdrożenia usługi Azure Cloud Services, dzięki czemu użytkownicy mogą określić klucz magazynu na potrzeby ustawiania rozszerzenie diagnostyki, w przypadku publikowania na platformie Azure w ciągłej integracji i Wdrożenie.
- Wprowadziliśmy jego możliwość przechowywania bezpiecznych parametrów połączenia i tokenizacji dla platformy Azure Diagnostyka (WAD), aby pomóc w rozwiązywaniu problemów z konfiguracją dla środowiska.
 
### <a name="windows-server-2016-virtual-machines"></a>Maszyny wirtualne systemu Windows Server 2016

- Program Visual Studio obsługuje teraz wdrażanie usług Cloud Services z maszynami wirtualnymi 5 rodziny systemów operacyjnych (Windows Server 2016). Dla istniejących usług w chmurze możesz zmienić ustawienia, aby docelowe nowe rodziny systemów operacyjnych. Podczas tworzenia nowych usług w chmurze, jeśli zdecydujesz się utworzyć usługę za pomocą programu .net 4.6 lub nowszej, zostaną domyślnie Usługa do użycia w 5 rodziny systemów operacyjnych.  Aby uzyskać więcej informacji, możesz przejrzeć [rodziny systemów operacyjnych gościa obsługuje tabeli](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/).

#### <a name="known-issues"></a>Znane problemy

- Zestaw Azure .NET SDK 2.9.6 wprowadzono ograniczenie, która blokuje wdrażania projektów przy użyciu nieobsługiwanego platformy .NET (np. .NET 4.6) do dowolnej rodziny systemów operacyjnych < 5. Udostępniono obejście [tutaj](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

 
### <a name="azure-in-role-cache"></a>Pamięć podręczna oparta na roli systemu Azure 

- Obsługa kończy się pamięć podręczna oparta na roli platformy Azure, w dniu 30 listopada 2016 r. Aby uzyskać więcej informacji, kliknij przycisk [tutaj](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Szablony usługi Azure Resource Manager dla usługi Azure Stack

- Wprowadziliśmy komunikaty usługi Azure Stack jako cel wdrożenia dla szablonów usługi Azure Resource Manager.


## <a name="azure-sdk-for-net-29-summary"></a>Zestaw Azure SDK dla platformy .NET 2.9 podsumowanie

## <a name="overview"></a>Przegląd
Ten dokument zawiera informacje o wersji zestawu Azure SDK dla wersji środowiska .NET 2.9. 

Aby uzyskać szczegółowe informacje o aktualizacji w tej wersji, zobacz [zestaw Azure SDK 2.9 ogłoszenie wpisu](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Zestaw Azure SDK 2.9 dla Visual Studio 2015 Update 2 i programu Visual Studio "15" w wersji zapoznawczej
Ta aktualizacja obejmuje następujące poprawki:

* Problem związany z generowania klienta REST API w którym ciąg "Unknown Type" pojawiał się jako nazwa folderu generacji kodu i/lub nazwę przestrzeni nazw upuszczanej do generowanego kodu.
* Problem związany z zaplanowanymi zadaniami Webjob, w której informacje uwierzytelniające nie mają być przekazane do usługi Scheduler, w procesie inicjowania obsługi.

Ta aktualizacja obejmuje następującą nową funkcję:

* Wsparcie dla drugorzędnych usług App Services karcie "Usługi" w oknie dialogowym aprowizacji usługi App Service. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Azure Data Lake Tools for Visual Studio 2015 Update 2
Te aktualizacje obejmują:

* **Azure Data Lake Tools** dla programu Visual Studio teraz zostanie scalona z zestawu Azure SDK dla wersji platformy .NET. Narzędzie jest automatycznie instalowany podczas instalowania zestawu Azure SDK. 
  
    Narzędzie jest często aktualizowany, przejdź [tutaj](http://aka.ms/datalaketool) pobierania aktualizacji.
* **Eksplorator serwera** umożliwia teraz wyświetlanie wszystkich i Utwórz niektóre jednostki metadanych języka U-SQL. Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blogu.

## <a name="hdinsight-tools"></a>Narzędzia HDInsight
**Narzędzia HDInsight** dla programu Visual Studio teraz obsługuje HDInsight w wersji 3.3, w tym wyświetlane na wykresach Tez i innym języku poprawki.

## <a name="azure-resource-manager"></a>Azure Resource Manager
W tej wersji dodano [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) Obsługa szablonów usługi Resource Manager.

## <a name="see-also"></a>Zobacz także
[Azure SDK 2.9 ogłoszenie wpisu](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

