---
title: Użyj podglądu zdarzeń w witrynie sieci Web | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób tworzenia witryny sieci Web platformy Microsoft Azure, z formantem Visual Studio podglądu raportów, które powoduje wyświetlenie raportu przechowywane na maszyny wirtualnej platformy Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: af8a4a9c25005925bed3ddb78ced618e669f7f09
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31424622"
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Korzystanie z narzędzia ReportViewer w witrynie internetowej hostowanej na platformie Azure
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Można tworzyć witryny sieci Web platformy Microsoft Azure, z formantem Visual Studio podglądu raportów, które powoduje wyświetlenie raportu przechowywane na maszyny wirtualnej platformy Microsoft Azure. Formant podglądu raportów jest w aplikacji sieci Web kompilacji przy użyciu szablonu aplikacji sieci Web ASP.NET.

> [!IMPORTANT]
> Szablony aplikacji sieci Web platformy ASP.NET MVC nie obsługują formantu ReportViewer.

Aby dołączyć podglądu raportów do witryny sieci Web platformy Microsoft Azure, należy wykonać następujące zadania.

* **Dodaj** zestawów do pakietu wdrożeniowego
* **Skonfiguruj** uwierzytelniania i autoryzacji
* **Publikowanie** aplikacji sieci Web ASP.NET na platformie Azure

## <a name="prerequisites"></a>Wymagania wstępne
Zapoznaj się z sekcją "ogólne zalecenia i najlepsze rozwiązania w zakresie" w [analizy biznesowej programu SQL Server w usłudze Azure Virtual Machines](../classic/ps-sql-bi.md).

> [!NOTE]
> Kontrolki podglądu raportów są dostarczane z programem Visual Studio, Standard Edition lub nowszy. Jeśli korzystasz z sieci Web Developer Express Edition, musisz zainstalować [RUNTIME 2012 PODGLĄDU raportów firmy MICROSOFT](https://www.microsoft.com/download/details.aspx?id=35747) możliwości korzystania z funkcji obsługi podglądu raportów.
> 
> W trybie przetwarzania lokalnego podglądu raportów nie jest obsługiwana w systemie Microsoft Azure.

## <a name="adding-assemblies-to-the-deployment-package"></a>Dodawanie zestawów do pakietu wdrożeniowego
Kiedy host ASP.NET aplikacji lokalnej, zestawy podglądu raportów bezpośrednio w globalnej pamięci podręcznej zestawów (GAC) serwera IIS zwykle są instalowane podczas instalacji programu Visual Studio i jest możliwy bezpośrednio przez aplikację. Jednak kiedy host aplikacji ASP.NET w chmurze, Microsoft Azure nie zezwala na niczego do zainstalowania w pamięci podręcznej GAC, dlatego należy się upewnić, że zestawy podglądu raportów są dostępne lokalnie dla aplikacji. Można to zrobić przez dodanie odwołania do nich w projekcie i skonfigurować je do skopiowania lokalnie.

W trybie przetwarzania zdalnego formantu ReportViewer używa następujących zestawów:

* **Microsoft.ReportViewer.WebForms.dll**: zawiera kod, podglądu raportów, należy użyć podglądu raportów na stronie. Odwołanie do tego zestawu jest dodawany do projektu po upuszczeniu formant podglądu raportów na stronę ASP.NET w projekcie.
* **Microsoft.ReportViewer.Common.dll**: zawiera klasy używany przez formant podglądu raportów w czasie wykonywania. Nie został automatycznie dodany do projektu.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Aby dodać odwołanie do Microsoft.ReportViewer.Common
* Kliknij prawym przyciskiem myszy projektu **odwołania** a następnie wybierz węzeł **Dodaj odwołanie**, wybierz zestaw na karcie .NET i kliknij przycisk **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Aby udostępnić zestawy lokalnie przez aplikację ASP.NET
1. W **odwołania** folderu, kliknij przycisk zestawu Microsoft.ReportViewer.Common, dzięki czemu jego właściwości są wyświetlane w okienku właściwości.
2. W okienku właściwości ustaw **Kopiuj lokalnie** na wartość True.
3. Powtórz kroki 1 i 2 dla Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Aby pobrać pakiet językowy podglądu raportów
1. Zainstaluj odpowiedni pakiet redystrybucyjny Runtime 2012 podglądu raportów firmy Microsoft z [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=317386).
2. Wybierz język z listy rozwijanej, a strona jest kierowany do odpowiedniej strony Centrum pobierania.
3. Kliknij przycisk **Pobierz** aby rozpocząć pobieranie ReportViewerLP.exe.
4. Po pobraniu ReportViewerLP.exe kliknij **Uruchom** natychmiast zainstalować, lub kliknij przycisk **zapisać** zapisać go w komputerze. Jeśli klikniesz przycisk **zapisać**, Zapamiętaj nazwę folderu, w którym możesz zapisać plik.
5. Zlokalizuj folder, w którym został zapisany plik. Kliknij prawym przyciskiem myszy ReportViewerLP.exe, kliknij przycisk **Uruchom jako administrator**, a następnie kliknij przycisk **tak**.
6. Po uruchomieniu ReportViewerLP.exe, zobaczysz, pliki zasobów ma c:\windows\assembly **Microsoft.ReportViewer.Webforms.Resources** i **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Aby skonfigurować zlokalizowany kontrolki podglądu raportów
1. Pobierz i zainstaluj pakiet redystrybucyjny Runtime 2012 podglądu raportów firmy Microsoft zgodnie z instrukcjami powyżej określony.
2. Utwórz <language> folderu projektu oraz kopiowania zestawu zasobów plików istnieje. Pliki zestawu zasobów do skopiowania: **Microsoft.ReportViewer.Webforms.Resources.dll** i **Microsoft.ReportViewer.Common.Resources.dll**. Wybierz pliki zestawu zasobów, a następnie w okienku właściwości ustaw **Kopiuj do katalogu wyjściowego** do "**skopiuj zawsze**".
3. Ustawienie kultury & UICulture dla projektu sieci web. Aby uzyskać więcej informacji na temat ustawiania kultury i kultury interfejsu użytkownika dla strony sieci Web programu ASP.NET, zobacz [porady: ustawienie kultury i kultury interfejsu użytkownika dla globalizacji strony sieci Web ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Konfigurowanie uwierzytelniania i autoryzacji
ReportViewer musi używać odpowiednie poświadczenia umożliwiające uwierzytelnianie w usłudze serwera raportów, a poświadczenia muszą być autoryzowane przez serwer raportów do raportów, które mają dostęp. Aby uzyskać informacje na temat uwierzytelniania, zobacz oficjalny dokument [raportu usług Reporting Services, Microsoft Azure maszyny wirtualnej na podstawie raportu serwery i formant podglądu](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publikowanie aplikacji sieci Web programu ASP.NET na platformie Azure
Aby uzyskać instrukcje na temat publikowania aplikacji sieci Web programu ASP.NET na platformie Azure, zobacz [porady: migracji i publikowanie aplikacji sieci Web na platformie Azure w programie Visual Studio](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) i [wprowadzenie do aplikacji sieci Web i ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> W menu skrótów w Eksploratorze rozwiązań nie ma polecenia Dodaj projektu wdrożenia platformy Azure lub Dodaj projekt usługi w chmurze Azure, konieczne może zmienić platformę docelową projektu na program .NET Framework 4.
> 
> Dwa polecenia mają zasadniczo te same funkcje. Co najmniej inne polecenie będą wyświetlane w menu skrótów w zależności od wersji zestawu SDK platformy Microsoft Azure został zainstalowany.
> 
> 

## <a name="resources"></a>Zasoby
[Raporty firmy Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server Business Intelligence na maszynach wirtualnych Azure](../classic/ps-sql-bi.md)

[Korzystanie z programu PowerShell do tworzenia maszyny wirtualnej platformy Azure z serwerem raportów pracującym w trybie macierzystym](../classic/ps-sql-report.md)

