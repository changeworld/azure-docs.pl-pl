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
ms.openlocfilehash: 30013c0bd8ded31ccaf3cdc142bbf8dc6b375031
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150961"
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Informacje o wersji zestawu Azure SDK dla programu .NET 2.9

Ten temat zawiera informacje o wersji dla wersji 2.9 i 2.9.6 zestawu Azure SDK dla platformy .NET.

## <a name="azure-sdk-for-net-296-release-summary"></a>Podsumowanie wersji zestawu Azure SDK dla platformy .NET 2.9.6

Data wydania: 2016-11-16
 
W tej wersji zostały wprowadzone żadne zmiany podziału Azure SDK 2.9. Nie ma również żadnych procesów uaktualniania konieczne korzystać z tego zestawu SDK z istniejącymi projektami usługi w chmurze.

### <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 Release Candidate

- W programie Visual Studio RC 2017 tej wersji zestawu Azure SDK dla platformy .NET jest wbudowany w obciążenia Azure. Wszystkie narzędzia należy wykonać rozwoju platformy Azure będzie częścią programu Visual Studio RC 2017 idąc dalej. Dla programu Visual Studio 2015 i Visual Studio 2013 zestawu SDK będą nadal dostępne za pośrednictwem WebPI. Firma Microsoft będzie roku zestawu Azure SDK dla wersji platformy .NET dla programu Visual Studio 2013, gdy program Visual Studio 2017 zwalnia jako produktu końcowego. Wykonaj to łącze, aby pobrać program Visual Studio RC 2017: https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Diagnostyka Azure

- Zmienić zachowanie, które będzie przechowywać parametry połączenia z częściowa tylko przy użyciu klucza zastępuje token dla parametrów połączenia magazynu diagnostyki usługi w chmurze. Klucz magazynu rzeczywista są obecnie przechowywane w folderze profilu użytkownika, można kontrolować dostęp do niej. Visual Studio odczyta klucza magazynu z folderu profilu użytkownika dla debugowania lokalnego i proces publikowania. 
- W odpowiedzi na zmianę opisane powyżej zespołu Visual Studio Online rozszerzone szablon zadania wdrożenia usługi w chmurze Azure, użytkownicy można określić klucz magazynu do ustawiania rozszerzenia diagnostyki podczas publikowania na platformie Azure w ciągłej integracji i wdrażania.
- Ułatwiliśmy możliwość przechowywania i tokenizacji dla Azure Diagnostics (WAD), aby ułatwić rozwiązywanie problemów z konfiguracją w environements ciąg bezpiecznego połączenia.
 
### <a name="windows-server-2016-virtual-machines"></a>Maszyny wirtualne systemu Windows Server 2016

- Program Visual Studio teraz obsługuje wdrażanie usługi w chmurze do maszyn wirtualnych 5 rodziny systemu operacyjnego (Windows Server 2016). Istniejących usług w chmurze można zmienić ustawienia pod kątem nowych rodziny systemów operacyjnych. Podczas tworzenia nowych usług w chmurze, jeśli użytkownik chce utworzyć usługę przy użyciu platformy .net 4.6 lub nowszej, będzie on domyślnej usługi do 5 rodziny systemu operacyjnego.  Aby uzyskać więcej informacji, możesz przejrzeć [rodziny systemów operacyjnych gościa obsługuje tabeli](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/).

#### <a name="known-issues"></a>Znane problemy

- Azure .NET SDK 2.9.6 wprowadzone ograniczeń, która blokuje wdrożenie projektów przy użyciu nieobsługiwanego platformy .NET (np. .NET 4.6) do dowolnego rodziny systemów operacyjnych < 5. Udostępniono obejście [tutaj](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

 
### <a name="azure-in-role-cache"></a>Pamięć podręczna na roli Azure 

- Obsługa kończy się na roli Azure w pamięci podręcznej w dniu 30 listopada 2016 r. Aby uzyskać więcej informacji, kliknij przycisk [tutaj](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Stack — szablony usługi Azure Resource Manager dla platformy Azure

- Dodaliśmy stosu Azure jako cel wdrożenia szablonów usługi Azure Resource Manager.


## <a name="azure-sdk-for-net-29-summary"></a>Zestaw Azure SDK dla platformy .NET 2.9 podsumowanie

## <a name="overview"></a>Przegląd
Ten dokument zawiera informacje o wersji dla zestawu Azure SDK dla platformy .NET 2.9 wydania. 

Aby uzyskać szczegółowe informacje na temat aktualizacji w tej wersji, zobacz [post anonsu zestaw Azure SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Zestaw Azure SDK 2.9 dla programu Visual Studio 2015 Update 2 do programu Visual Studio "15" Preview
Ta aktualizacja obejmuje następujące poprawki:

* Problem związany z REST API generowania klienta w, w którym ciąg "Nieznany typ" zostanie wyświetlony jako nazwa folderu generacji kodu i/lub nazwę przestrzeni nazw upuścić na wygenerowany kod.
* Problem związany z zaplanowanych zadań Webjob, w którym sprawdzano informacje dotyczące uwierzytelniania do przekazania do harmonogramu, w procesie inicjowania obsługi.

Ta aktualizacja obejmuje następującą nową funkcję:

* Obsługa dodatkowej usługi aplikacji na karcie "Usługi" okno dialogowe zastrzegania usługi aplikacji. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Azure Data Lake Tools for Visual Studio 2015 Update 2
Tej aktualizacji obejmuje następujące funkcje:

* **Azure Data Lake Tools** dla programu Visual Studio teraz jest scalany zestawu Azure SDK dla wersji platformy .NET. Narzędzie jest automatycznie instalowany podczas instalowania zestawu Azure SDK. 
  
    Narzędzie jest często aktualizowana, przejdź [tutaj](http://aka.ms/datalaketool) pobierania aktualizacji.
* **W Eksploratorze serwera** umożliwia teraz wyświetlanie wszystkich i tworzenie niektóre jednostki metadanych U-SQL. Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blogu.

## <a name="hdinsight-tools"></a>Narzędzia HDInsight Tools
**Narzędzia HDInsight Tools** dla programu Visual Studio teraz obsługuje HDInsight wersji 3.3, łącznie z wykazem wykresach Tez i inny język poprawki.

## <a name="azure-resource-manager"></a>Azure Resource Manager
Ta wersja dodaje [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) obsługę szablony Menedżera zasobów.

## <a name="see-also"></a>Zobacz także
[Azure SDK 2.9 anonsu post](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

