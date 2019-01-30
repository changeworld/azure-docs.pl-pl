---
title: Usługi Azure Stack Development Kit podstawy | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wykonywania zadań administracyjnych podstawowe dla usługi Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 79e031226f11d45121827795c34b0103235d8d79
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55248167"
---
# <a name="asdk-administration-basics"></a>Podstawy administracji ASDK 
Istnieje kilka kwestii, o których należy wiedzieć, jeśli jesteś nowym użytkownikiem usługi Azure Stack Development Kit (ASDK) Administracja. Niniejsze wskazówki zawiera omówienie Twojej roli jako operatorów usługi Azure Stack w środowisku oceny i jak zapewnić użytkownikom testów może stać się produktywność szybko.

Najpierw należy przejrzeć [co to jest Azure Stack Development Kit?](asdk-what-is.md) artykuł, aby upewnić się, że zrozumienie przeznaczenia ASDK i jego pewne ograniczenia. Zestaw development kit należy używać jako "piaskownicy", gdzie możesz ocenić usługi Azure Stack, aby tworzyć i testować aplikacje w środowisku nieprodukcyjnym. 

Takich jak Azure Azure Stack innovates szybko dzięki regularnie udostępnimy nowe kompilacje ASDK. Jednak nie można uaktualnić ASDK, jak w przypadku wdrożeń systemów zintegrowanych w usłudze Azure Stack. Tak, jeśli chcesz przejść do najnowszej kompilacji, należy całkowicie [ponownie wdrożyć ASDK](asdk-redeploy.md). Nie można zastosować pakietów aktualizacji. Ten proces trwa, ale korzyścią jest to, że możesz wypróbować najnowsze funkcje jak najszybciej po ich udostępnieniu. 

## <a name="what-account-should-i-use"></a>Z jakiego konta mam korzystać?
Istnieje kilka istotnych kwestii konta, których należy wiedzieć podczas zarządzania usługi Azure Stack. Szczególnie w przypadku wdrożeń przy użyciu systemu Windows Server Active Directory Federation Services (AD FS) jako dostawcy tożsamości, a nie usługi Azure Active Directory (Azure AD). Obowiązują następujące zastrzeżenia konta do usługi Azure Stack zintegrowane systemy i ASDK wdrożeń:

|Konto|Azure AD|AD FS|
|-----|-----|-----|
|Administrator lokalny (. \Administrator)|Administrator hosta ASDK|Administrator hosta ASDK|
|AzureStack\AzureStackAdmin|Administrator hosta ASDK<br><br>Może służyć do logowania do portalu administracyjnego usługi Azure Stack<br><br>Dostęp do wyświetlania i administrować pierścieni usługi Service Fabric|Administrator hosta ASDK<br><br>Brak dostępu do portalu administracyjnego usługi Azure Stack<br><br>Dostęp do wyświetlania i administrować pierścieni usługi Service Fabric<br><br>Nie jest już właściciela z subskrypcji domyślny dostawca (DPS)|
|AzureStack\CloudAdmin|Można uzyskać dostęp, a następnie uruchom polecenia dozwolonych w uprzywilejowanych punktu końcowego|Można uzyskać dostęp, a następnie uruchom polecenia dozwolonych w uprzywilejowanych punktu końcowego<br><br>Nie można zalogować się w hoście ASDK<br><br>Właściciel subskrypcji dostawcy domyślnego (DPS)|
|Administrator globalny usługi Azure AD|Używany podczas instalacji<br><br>Właściciel subskrypcji dostawcy domyślnego (DPS)|Nie dotyczy|
|

## <a name="what-tools-do-i-use-to-manage"></a>Jakie narzędzia należy używać do zarządzania?
Możesz użyć [Portal administratora stosu Azure](https://adminportal.local.azurestack.external) lub programu PowerShell do zarządzania usługi Azure Stack. Najprostszym sposobem podstawowe informacje na temat jest za pośrednictwem portalu. Jeśli chcesz użyć programu PowerShell, musisz zainstalować [programu PowerShell dla usługi Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) i [pobieranie narzędzia Azure Stack z usługi GitHub](asdk-post-deploy.md#download-the-azure-stack-tools).

Usługa Azure Stack jako jego podstawowy mechanizm wdrażania, zarządzania i organizacji korzysta z usługi Azure Resource Manager. Jeśli użytkownik chce zarządzać usługi Azure Stack oraz pomóc je obsługi użytkowników, można dowiedzieć się o usłudze Azure Resource Manager. Dowiedz się więcej, czytając [wprowadzenie do usługi Azure Resource Manager oficjalny dokument dotyczący](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Twoje obowiązki typowe
Użytkownicy chcą korzystać z usług. Ich względem Twojej roli głównego jest udostępnienia im tych usług. Używanie ASDK, możesz dowiedzieć się które usług do zaoferowania i sposób wprowadzania tych usług dostępnych przez [Tworzenie planów, ofert i przydziałów](asdk-offer-services.md). Należy również dodać elementy do portalu marketplace, takich jak obrazy maszyn wirtualnych. Najprostszym sposobem jest [pobieranie elementów portalu marketplace](asdk-marketplace-item.md) z platformy Azure do usługi Azure Stack.

> [!NOTE]
> Jeśli chcesz przetestować plany, oferty i usług, należy użyć [portal użytkowników](https://portal.local.azurestack.external); [portal administratora](https://adminportal.local.azurestack.external).

Oprócz zapewniania usług, należy wykonać wszystkie regularne obowiązki operatora usługi Azure Stack zapewnienie ASDK pracę. Tych zadań obejmują elementy, jak pokazano poniżej:
- Dodaj konta użytkowników dla usługi Azure Active Directory (Azure AD) lub wdrożeniach usługi Active Directory Federation Services (AD FS).
- Przypisywanie dostępu opartej na rolach ról kontroli (RBAC) (nie jest ograniczony do administratorów po prostu)
- Monitoruj kondycję infrastruktury
- Zarządzanie zasobami magazynu i sieci
- Wymiana sprzętu komputera hosta kit rozwoju nie powiodło się 

## <a name="where-to-get-support"></a>Gdzie można uzyskać pomoc techniczną
Dla zestawu SDK, jedynym rozwiązaniem pomocy technicznej jest pytania dotyczące pomocy technicznej w [forum usługi Microsoft Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Jeśli kliknij ikonę Pomoc i obsługa techniczna (znak zapytania) w prawym górnym rogu portalu administratora, a następnie kliknij przycisk **nowe żądanie obsługi**, otworzy się w witrynie forów bezpośrednio. Monitorowane są regularnie forów. 

> [!IMPORTANT]
> Ponieważ ASDK jest środowisko wersji ewaluacyjnej, nie jest oficjalnym obsługiwane oferowane za pośrednictwem usług obsługi klienta firmy Microsoft (CSS).

## <a name="next-steps"></a>Kolejne kroki
[Wdrażanie ASDK](asdk-install.md)

