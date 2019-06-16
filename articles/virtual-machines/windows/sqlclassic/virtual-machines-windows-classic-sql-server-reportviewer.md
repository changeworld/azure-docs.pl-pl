---
title: Korzystanie z narzędzia ReportViewer w witrynie sieci Web | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób tworzenia witryny sieci Web platformy Microsoft, za pomocą kontrolki ReportViewer usługi Visual Studio, która wyświetla raport przechowywane na maszynie wirtualnej platformy Microsoft.
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
ms.openlocfilehash: b554dc1fa33519d87aa0c9c5ba9130b47cbea142
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60580072"
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Korzystanie z narzędzia ReportViewer w witrynie internetowej hostowanej na platformie Azure
> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Możesz tworzyć witryny sieci Web platformy Microsoft, za pomocą kontrolki ReportViewer usługi Visual Studio, która wyświetla raport przechowywane na maszynie wirtualnej platformy Microsoft. Kontrolka ReportViewer jest w aplikacji sieci Web kompilacji przy użyciu szablonu aplikacji sieci Web platformy ASP.NET.

> [!IMPORTANT]
> Szablony aplikacji sieci Web programu ASP.NET MVC nie obsługują formantu ReportViewer.

Włączanie podglądu raportów do witryny sieci Web platformy Microsoft, należy wykonać poniższe zadania.

* **Dodaj** zestawy do pakietu wdrożeniowego
* **Konfigurowanie** uwierzytelnianie i autoryzacja
* **Publikowanie** aplikacji sieci Web platformy ASP.NET na platformie Azure

## <a name="prerequisites"></a>Wymagania wstępne
Zapoznaj się z sekcją "ogólne zalecenia i najlepsze rozwiązania" w [analiza biznesowa programu SQL Server w usłudze Azure Virtual Machines](../classic/ps-sql-bi.md).

> [!NOTE]
> Kontrolki podglądu raportów są dostarczane z programem Visual Studio, Standard Edition lub nowszej. Jeśli używasz Web Developer Express Edition, musisz zainstalować [RUNTIME 2012 PODGLĄDU raportów firmy MICROSOFT](https://www.microsoft.com/download/details.aspx?id=35747) korzystanie z funkcji środowiska uruchomieniowego podglądu zdarzeń.
>
> Skonfigurowane w trybie przetwarzania lokalnego podglądu raportów nie jest obsługiwana w systemie Microsoft Azure.

## <a name="adding-assemblies-to-the-deployment-package"></a>Dodawanie zestawów do pakietu wdrożeniowego
Hostowanie ASP.NET aplikacji lokalnych zestawów podglądu raportów bezpośrednio w globalnej pamięci podręcznej zestawów (GAC) serwera usług IIS zwykle są instalowane podczas instalacji programu Visual Studio i jest możliwy bezpośrednio przez aplikację. Jednakże hostowanie aplikacji ASP.NET w chmurze Microsoft Azure nie zezwala na coś, żeby można zainstalować w GAC, więc należy się upewnić, że zestawy podglądu raportów są dostępne lokalnie dla aplikacji. Można to zrobić, dodając odwołania do tych w projekcie i skonfigurować je do skopiowania lokalnie.

W trybie przetwarzania zdalnego Kontrolka ReportViewer używa następujących zestawów:

* **Microsoft.ReportViewer.WebForms.dll**: Zawiera kod, podglądu raportów, należy użyć podglądu raportów na stronie. Odwołanie do tego zestawu jest dodawane do projektu, gdy upuścisz suszarkę kontrolki ReportViewer na stronie ASP.NET w projekcie.
* **Microsoft.ReportViewer.Common.dll**: Zawiera klasy używane przez kontrolkę ReportViewer w czasie wykonywania. Nie został automatycznie dodany do projektu.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Aby dodać odwołanie do Microsoft.ReportViewer.Common
* Kliknij prawym przyciskiem myszy projektu **odwołania** a następnie wybierz węzeł **Dodaj odwołanie**w karcie .NET wybierz zestaw i kliknij przycisk **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Aby udostępnić zestawy lokalnie w aplikacji ASP.NET
1. W **odwołania** folderu, kliknij przycisk zestawu Microsoft.ReportViewer.Common, dzięki czemu jego właściwości są wyświetlane w okienku właściwości.
2. W okienku właściwości ustaw **Kopiuj lokalnie** na wartość True.
3. Powtórz kroki 1 i 2 dla Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Aby uzyskać pakiet językowy podglądu raportów
1. Zainstaluj odpowiedni pakiet redystrybucyjny Runtime 2012 podglądu raportów firmy Microsoft z [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=317386).
2. Wybierz język z listy rozwijanej, a strona jest przekierowywany do odpowiedniej strony Centrum pobierania.
3. Kliknij przycisk **Pobierz** aby rozpocząć pobieranie ReportViewerLP.exe.
4. Po pobraniu ReportViewerLP.exe, kliknij przycisk **Uruchom** od razu zainstalować, lub kliknąć przycisk **Zapisz** zapisać go na komputerze. Jeśli klikniesz **Zapisz**, należy pamiętać, nazwę folderu, w której plik zostanie zapisany.
5. Zlokalizuj folder, w którym został zapisany plik. Kliknij prawym przyciskiem myszy ReportViewerLP.exe, kliknij przycisk **Uruchom jako administrator**, a następnie kliknij przycisk **tak**.
6. Po uruchomieniu ReportViewerLP.exe, zostanie wyświetlony c:\windows\assembly ma pliki zasobów **Microsoft.ReportViewer.Webforms.Resources** i **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Aby skonfigurować zlokalizowany kontrolki podglądu raportów
1. Pobierz i zainstaluj pakiet redystrybucyjny Runtime 2012 podglądu raportów firmy Microsoft zgodnie z instrukcjami powyżej określony.
2. Tworzenie \<języka\> folderu w projekcie i kopiowania zestawu skojarzonego zasobu pliki. Pliki zasobów zestawu do skopiowania są: **Microsoft.ReportViewer.Webforms.Resources.dll** i **Microsoft.ReportViewer.Common.Resources.dll**. Wybierz pliki zestawu zasobów, a w okienku właściwości ustaw **Kopiuj do katalogu wyjściowego** do "**zawsze Kopiuj**".
3. Ustaw Culture i UICulture dla projektu sieci web. Aby uzyskać więcej informacji na temat sposobu ustawiania kultury i kultury UI dla strony sieci Web platformy ASP.NET, zobacz [jak: Ustawianie kultury i kultury UI dla globalizacji strony sieci Web platformy ASP.NET](https://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Konfigurowanie uwierzytelniania i autoryzacji
ReportViewer musi używać prawidłowe poświadczenia do uwierzytelniania za pomocą serwera raportów, a poświadczenia muszą być autoryzowane przez serwer raportów, raportów, które mają dostęp do. Aby uzyskać informacje na temat uwierzytelniania, zobacz oficjalny dokument [raportu usług Reporting Services w kontrolce podglądu i serwerami raportów w usłudze Microsoft Azure maszyny wirtualnej na podstawie](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publikowanie aplikacji sieci Web platformy ASP.NET na platformie Azure
Aby uzyskać instrukcje na temat publikowania aplikacji internetowej ASP.NET na platformie Azure, zobacz [jak: Migracja i publikowanie aplikacji sieci Web na platformie Azure z programu Visual Studio](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) i [Rozpoczynanie pracy z usługą Web Apps i ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> W menu skrótów w oknie Eksploratora rozwiązań nie ma polecenia Dodaj projekt wdrożenia platformy Azure lub dodać projekt usługi w chmurze platformy Azure, konieczne może zmienić platformę docelową dla projektu do .NET Framework 4.
>
> Dwa polecenia zapewniają zasadniczo taką samą funkcjonalność. Co najmniej inne polecenia pojawi się w menu skrótów zależności od instalowanej wersji zestawu SDK usługi Microsoft Azure został zainstalowany.
>
>

## <a name="resources"></a>Zasoby
[Raporty firmy Microsoft](https://go.microsoft.com/fwlink/?LinkId=205399)

[Analiza biznesowa programu SQL Server na maszynach wirtualnych platformy Azure](../classic/ps-sql-bi.md)

[Korzystanie z programu PowerShell do tworzenia maszyny wirtualnej platformy Azure z serwerem raportów pracującym w trybie macierzystym](../classic/ps-sql-report.md)
