---
title: Informacje o wersji zestawu Azure SDK dla platformy .NET 2.8
description: Informacje o wersji zestawu Azure SDK dla platformy .NET 2.8
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
ms.openlocfilehash: 6aa2684a900dffecd481d51b8876b0e674c1a6ea
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235534"
---
# <a name="azure-sdk-for-net-28-281-and-282"></a>Zestaw Azure SDK dla platformy .NET 2.8, 2.8.1 i 2.8.2
## <a name="overview"></a>Przegląd
Ten artykuł zawiera informacje o wersji, (które znane problemy i przełomowe zmiany w tym) z zestawu Azure SDK dla platformy .NET 2.8 2.8.1 i 2.8.2 wydania. 

Aby uzyskać pełną listę nowych funkcji i aktualizacji wprowadzonych w tej wersji, zobacz [Azure SDK 2.8 dla programu Visual Studio 2013 i Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) anonsu. 

## <a name="azure-sdk-for-net-28"></a>Azure SDK for .NET 2.8
### <a name="download-azure-sdk-for-net-28"></a>Pobierz zestaw Azure SDK dla platformy .NET 2.8
[Zestaw Azure SDK dla platformy .NET 2.8 for Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=699285) 

[Zestaw Azure SDK dla platformy .NET 2.8 for Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=699287)

### <a name="net-452-support"></a>Obsługa .NET 4.5.2
#### <a name="known-issues"></a>Znane problemy
Azure .NET SDK 2.8 pozwala na tworzenie .NET 4.5.2 pakiety usługi w chmurze. Jednak .NET 4.5.2 framework nie zostaną zainstalowane na domyślnym wydania obrazów systemu operacyjnego gościa do stycznia 2016 roku systemu operacyjnego gościa. Przed tym, .NET 4.5.2 framework będą dostępne przy użyciu oddzielnych wersji wersji systemu operacyjnego gościa — listopad 2015-02. Zobacz [wersji systemu operacyjnego gościa platformy Azure i zgodności zestawów SDK](../cloud-services/cloud-services-guestos-update-matrix.md) strony, aby śledzić, kiedy obraz zostanie udostępniona.  Po zwolnieniu listopada 2015-02 obrazu można użyć tego obrazu, aktualizując plik (cscfg) pliku konfiguracji usługi w chmurze. W konfiguracji usługi pliku atrybut osVersion elementu ServiceConfiguration na ciąg "WA-GOŚCIA-OS-4.26_201511-02". Jeśli zdecydujesz się zgoda na korzystanie z tego obrazu będzie już uzyskać automatyczne aktualizacje systemu operacyjnego gościa. Aby uzyskać aktualizacje automatyczne wersję systemu operacyjnego musi być równa "*" i .NET 4.5.2 będą dostępne tylko za pomocą funkcji Aktualizacje automatyczne w styczniu 2016.

### <a name="azure-data-factory"></a>Azure Data Factory
#### <a name="known-issues"></a>Znane problemy
Podczas **szablon fabryki danych** tworzenia obejmujące przykładowe dane projektu, skrypt powłoki power platformy azure może zakończyć się niepowodzeniem w przypadku usługi azure power shell wersji zainstalowane na komputerze po 0.9.8.

Aby pomyślnie utworzyć tego typu projektu, musisz zainstalować [usługi azure power shell wersji 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).

### <a name="azure-resource-manager-tools"></a>Narzędzia usługi Azure Resource Manager
#### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
Skrypt programu PowerShell, dostarczone przez projekt grupy zasobów platformy Azure została zaktualizowana w tej wersji, aby pracować z nowych poleceń cmdlet programu Azure PowerShell w wersji 1.0.  Ten nowy skrypt nie będzie działać z programu Visual Studio podczas korzystania z zestawu SDK przed 2.8.  

Skrypty z projektów utworzonych w starszych wersjach zestawu SDK nie będzie działać z poziomu programu Visual Studio przy użyciu zestawu SDK 2.8.  Wszystkie skrypty będą w dalszym ciągu pracować poza programem Visual Studio z odpowiednią wersję poleceń cmdlet programu Azure PowerShell.  

Zestaw SDK 2.8 wymaga poleceń cmdlet programu Azure PowerShell w wersji 1.0.  Wszystkie wersje zestawu SDK wymaga wersji 0.9.8 poleceń cmdlet programu Azure PowerShell.  Aby uzyskać więcej informacji, zobacz [to](https://go.microsoft.com/fwlink/?LinkID=623011) blogu.

### <a name="web-tools-extensions"></a>Rozszerzeń narzędzi sieci Web
#### <a name="known-issues"></a>Znane problemy
Następujące znane problemy zostaną rozwiązane w wersji następujących.

* App Service związane z chmury i Eksplorator serwera gestu w środowiskach nieprodukcyjnych (np. Azure (Chiny) lub klienci usługi Azure Stack) nie działają. W przypadku klientów w tych obszarach których to dotyczy pobieranie profilu publikowania w witrynie Azure portal umożliwi możliwości publikowania. Przyszłej wersji zostanie napraw gestów takich jak "Dołączanie debugera" i "Wyświetl dzienniki przesyłania strumieniowego" dla klientów z Stack i Azure (Chiny). 
* Klienci mogą zostać wyświetlone błędy podczas tworzenia, gdy wystąpienie usługi App Insights, do którego jest wdrażana znajduje się w regionie innym niż wschodnie stany USA w usłudze App Service. W tych scenariuszach tworzenia usługi App Service w portalu i pobieranie profilu publikowania spowoduje włączenie scenariuszy publikowania. 

### <a name="azure-hdinsight-tools"></a>Narzędzia usługi Azure HDInsight
#### <a name="new-updates"></a>Nowe aktualizacje
* Możesz wykonywania zapytania programu Hive w klastrze za pośrednictwem serwera HiveServer2, z prawie nie obciążenie i wyświetlić dzienniki zadania w czasie rzeczywistym.
* Przy użyciu nowej gałęzi widok wykonywania zadania analizując bardziej zadania, Poznaj więcej szczegółów i identyfikowania potencjalnych problemów.

Aby uzyskać informacje, zobacz [Azure SDK 2.8 dla programu Visual Studio 2013 i Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Zestaw Azure SDK dla programu .NET 2.8.1
### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Znane problemy dotyczące programu Visual Studio 2013 i Visual Studio 2015
1. Publikuje miejsca zostanie wyzwolone zadania WebJob Pokaż i błąd i nie będzie zestaw harmonogramu, ale będzie umożliwiać wypychanie powiadomień zadania WebJob na platformie Azure. Klientów, którzy wymagają zadania zaplanowane służy następnie witryny Azure Portal, aby skonfigurować harmonogram dla zadania WebJob. 
2. Python mogą wystąpić problemy z debugera. Zespół usługi wprowadza poprawki dla tego, ale dotyczą klientów, daj Microsoft wiedzieć na forach lub ogłoszenie w blogu lub release notes sekcji komentarzy. 
3. Klienci, w niektórych regionach (np. Indie Południowe) będą napotykać błędy aprowizacji usługi App Service. Jest to zgodne z portalu, a klienci, którzy ten problem, można użyć witryny Azure portal umożliwia zażądanie dostępu publikowania do tych regionów geograficznych. Gdy będą one żądać dostępu do tych regionów przy użyciu portalu Azure aprowizacji powinny działać. 

## <a name="azure-sdk-for-net-282"></a>Zestaw Azure SDK dla platformy .NET 2.8.2
Po zainstalowaniu 2.8.2 narzędzia, klienci mogą wystąpić następujący problem.         

* Jeśli używasz systemu Windows 10 i nie jest zainstalowany program Internet Explorer, może wystąpić błąd "Nie można odnaleźć programu Internet Explorer".
  Aby rozwiązać ten problem, zainstaluj program Internet Explorer przy użyciu okna dialogowego Dodaj/Usuń składniki Windows.

Jeśli zauważysz ten problem, należy użyć funkcji Wyślij uśmiech Zgłoś ją.

Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) wpis.

## <a name="other-updates"></a>Inne aktualizacje
W przypadku innych aktualizacji, zobacz [Azure SDK 2.8 ogłoszenie wpisu](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="also-see"></a>Zobacz też
[Azure SDK 2.8 ogłoszenie wpisu](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Pomoc techniczna i informacje o wycofaniu dla zestawu Azure SDK dla platformy .NET i interfejsów API](https://msdn.microsoft.com/library/azure/dn479282.aspx)

