---
title: Zestaw Azure SDK dla platformy .NET 2,8 informacje o wersji
description: Zestaw Azure SDK dla platformy .NET 2,8 informacje o wersji
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: de7207ff-ba4f-4008-9141-8742fcaa3254
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 0b9f55d69c824e86245738a082f95fc529583f58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "23836392"
---
# <a name="azure-sdk-for-net-28-281-and-282"></a>Zestaw Azure SDK dla platformy .NET 2.8, 2.8.1 i 2.8.2
## <a name="overview"></a>Przegląd
Ten artykuł zawiera informacje o wersji (które obejmują znane problemy i fundamentalne zmiany) dla zestawu Azure SDK dla platformy .NET 2.8, 2.8.1 i 2.8.2 zwalnia. 

Aby uzyskać pełną listę nowych funkcji i aktualizacje wprowadzone w tej wersji, zobacz [Azure SDK 2.8 dla programu Visual Studio 2013 i Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) anonsu. 

## <a name="azure-sdk-for-net-28"></a>Azure SDK for .NET 2.8
### <a name="download-azure-sdk-for-net-28"></a>Pobierz zestaw Azure SDK dla platformy .NET 2.8
[Zestaw Azure SDK dla platformy .NET 2.8 dla programu Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Zestaw Azure SDK dla platformy .NET 2.8 dla programu Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)

### <a name="net-452-support"></a>Obsługuje platformy .NET w wersji 4.5.2
#### <a name="known-issues"></a>Znane problemy
Azure .NET SDK 2.8 służy do tworzenia .NET 4.5.2 pakiety usługi w chmurze. Jednak .NET 4.5.2 framework nie zostaną zainstalowane na domyślnym wersji obrazów systemu operacyjnego gościa do stycznia 2016 systemu operacyjnego gościa. Przed, 4.5.2 .NET framework będą dostępne przy użyciu oddzielnych wersji wersji systemu operacyjnego gościa — listopad 2015-02. Zobacz [poszczególnych wersji systemu operacyjnego gościa Azure i zgodność pakietu SDK](../cloud-services/cloud-services-guestos-update-matrix.md) stronę, aby śledzić, gdy obraz zostanie zwolniony.  Po zwolnieniu listopada 2015-02 obrazu można użyć tego obrazu, aktualizując pliku (cscfg) pliku konfiguracji usługi w chmurze. W konfiguracji usługi plik ustawiony atrybut osVersion elementu element ServiceConfiguration ciąg "WA-GOŚCIA-OS-4.26_201511-02". Jeśli użytkownik chce się użyć tego obrazu, a następnie nie będzie już pobrać automatycznej aktualizacji systemu operacyjnego gościa. Aby pobrać aktualizacje automatyczne osVersion musi mieć wartość "*" i .NET 4.5.2 będą dostępne tylko za pomocą funkcji Aktualizacje automatyczne w stycznia 2016.

### <a name="azure-data-factory"></a>Azure Data Factory
#### <a name="known-issues"></a>Znane problemy
Podczas **szablon fabryki danych** projektu tworzenia dotyczące przykładowych danych, skrypt powłoki azure power może zakończyć się niepowodzeniem, jeśli jest zainstalowany na komputerze wersji powłoki azure power po 0.9.8.

Aby pomyślnie utworzyć ten typ projektu, musisz zainstalować [azure power shell wersji 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).

### <a name="azure-resource-manager-tools"></a>Narzędzia Menedżera zasobów Azure
#### <a name="breaking-changes"></a>Fundamentalne zmiany
Skrypt programu PowerShell dostarczane przez projekt grupy zasobów platformy Azure został zaktualizowany w tej wersji do pracy z nowych poleceń cmdlet programu Azure PowerShell w wersji 1.0.  Ten nowy skrypt nie będzie działać z z programem Visual Studio podczas korzystania z zestawu SDK przed 2.8.  

Skrypty z projektów utworzonych w starszych wersjach zestawu SDK nie będzie uruchamiana z poziomu programu Visual Studio przy użyciu zestawu SDK 2.8.  Wszystkie skrypty będą nadal działać poza Visual Studio z odpowiednią wersją poleceń cmdlet programu Azure PowerShell.  

2.8 zestaw SDK wymaga wersji 1.0 poleceń cmdlet programu Azure PowerShell.  Wszystkie wersje zestawu SDK wymaga wersji 0.9.8 poleceń cmdlet programu Azure PowerShell.  Aby uzyskać więcej informacji, zobacz [to](http://go.microsoft.com/fwlink/?LinkID=623011) blogu.

### <a name="web-tools-extensions"></a>Rozszerzeń narzędzi sieci Web
#### <a name="known-issues"></a>Znane problemy
Następujące znane problemy zostaną rozwiązane w następujących wersji.

* Usługi aplikacji związane z chmury i Eksploratora serwera gestu w środowiskach nieprodukcyjnych (na przykład chińskiej wersji platformy Azure lub klienci stosu Azure) nie działają. Dla klientów w tych obszarach wpływ na pobieranie profilu publikowania z portalu Azure zostaną włączone możliwości publikowania. Przyszłych wydaniach naprawi gesty, takie jak "Dołączanie debugera" i "Wyświetl dzienniki przesyłania strumieniowego" dla Chińskiej wersji platformy Azure i klientami stosu. 
* Klienci mogą zostać wyświetlone błędy podczas tworzenia, gdy wystąpienie szczegółowych danych aplikacji, na którym jest wdrażany znajduje się w regionie innym niż wschodnie stany USA usługi aplikacji. W tych scenariuszach tworzenia usługi aplikacji w portalu i pobieranie profilu publikowania spowoduje włączenie publikowania scenariuszy. 

### <a name="azure-hdinsight-tools"></a>Usługa Azure HDInsight Tools
#### <a name="new-updates"></a>Nowe aktualizacje
* Można wykonać zapytanie Hive w klastrze za pośrednictwem serwera HiveServer2 o prawie nie koszty i zobacz dzienniki zadania czasie rzeczywistym.
* Przy użyciu nowego Hive widok wykonywania zadania mogą odnajdywać do zadania głębiej, więcej szczegółów znajduje i zidentyfikuj potencjalne problemy.

Aby uzyskać informacje, zobacz [Azure SDK 2.8 dla programu Visual Studio 2013 i Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Zestaw Azure SDK dla programu .NET 2.8.1
### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Znane problemy dotyczące programu Visual Studio 2013 i Visual Studio 2015
1. Publikuje wyzwalanych zadania WebJob do gniazda będzie Pokaż i błąd i nie będzie zestaw zgodnie z harmonogramem, ale przeprowadzi wypychanie zadania WebJob na platformie Azure. Klienci, którzy wymagają zaplanowane zadania można następnie skonfigurować harmonogram dla zadania WebJob za pomocą portalu Azure. 
2. Python klientów może zgłaszać problemy z debugera. Zespół usługi wprowadza poprawkę dla tego, ale dotyczy klientów, prosimy Microsoft wiedzą na forach lub na blogu anonsu lub release notes sekcji komentarzy. 
3. Klienci w pewnych regionach (na przykład Południowa, Indie) może wystąpić błędy udostępniania usługi aplikacji. Jest to zgodne z portalu, a klienci, którzy ten problem wystąpi, mogą korzystać z portalu Azure umożliwia zażądanie dostępu do publikowania tych regionów geograficznych. Po ich zażądać dostępu do tych regionów, przy użyciu portalu Azure udostępnianie powinny działać. 

## <a name="azure-sdk-for-net-282"></a>Zestaw Azure SDK dla platformy .NET 2.8.2
Po zainstalowaniu 2.8.2 narzędzia, klienci mogą wystąpić następujący problem.         

* Jeśli używasz systemu Windows 10 i nie jest zainstalowany program Internet Explorer, może wystąpić błąd "Nie można odnaleźć programu Internet Explorer".
  Aby rozwiązać ten problem, zainstaluj program Internet Explorer, korzystając z okna dialogowego Dodaj/Usuń składniki systemu Windows.

Jeśli zauważysz ten problem, należy użyć funkcji wysyłania a uśmiech do raportu.

Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) post.

## <a name="other-updates"></a>Inne aktualizacje
W przypadku innych aktualizacji, zobacz [post anonsu Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="also-see"></a>Zobacz też
[Azure SDK 2.8 anonsu post](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Obsługa i wycofania informacji dla zestawu Azure SDK dla platformy .NET i interfejsów API](https://msdn.microsoft.com/library/azure/dn479282.aspx)

