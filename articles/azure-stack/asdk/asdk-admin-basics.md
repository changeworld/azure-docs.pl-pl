---
title: Podstawowe informacje dotyczące stosu Azure programowanie zestawu | Dokumentacja firmy Microsoft
description: Opisuje sposób wykonywania zadań administracyjnych podstawowe dla Azure stosu Development Kit (ASDK).
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: fa2ee4df9a0a28c08237f30fcf6a4bac50c21100
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849512"
---
# <a name="asdk-administration-basics"></a>Podstawy administracji ASDK 
Istnieje kilka kwestii, które trzeba znać, jeśli zaczynasz administracji Azure stosu Development Kit (ASDK). W tych wskazówkach zawiera omówienie roli jako operator Azure stosu w środowisku oceny i jak zapewnić użytkownikom testu może stać się produktywności szybko.

Najpierw należy przejrzeć [co to jest Azure stosu Development Kit?](asdk-what-is.md) artykuł, aby upewnić się, zrozumienie przeznaczenia ASDK i jego ograniczenia. Zestaw deweloperski należy używać jako "piaskownicy", której będziesz w stanie ocenić stosu Azure umożliwiające opracowanie i przetestowanie aplikacji w środowiskach nieprodukcyjnych. 

Takich jak Azure Azure stosu innovates szybko tak regularnie udostępnimy nowe kompilacje ASDK. Jednak nie można uaktualnić ASDK jak stosu Azure zintegrowanych systemów wdrożeń. Tak, jeśli chcesz przejść do ostatniej kompilacji, należy całkowicie [ponownie wdrożyć ASDK](asdk-redeploy.md). Nie można zastosować pakietów aktualizacji. Ten proces trwa, ale korzyścią jest to, że można wypróbować najnowsze funkcje jak tylko staną się dostępne. 

## <a name="what-account-should-i-use"></a>Z jakiego konta mam korzystać?
Istnieje kilka uwagi dotyczące konta, które należy zwrócić uwagę podczas zarządzania Azure stosu. Szczególnie w przypadku wdrożeń za pomocą systemu Windows Server Active Directory Federation Services (AD FS) jako dostawca tożsamości, a nie usługi Azure Active Directory (Azure AD). Następujące uwagi dotyczące konta dotyczą zarówno stosu Azure zintegrowanych systemów i ASDK wdrożenia:

|Konto|Azure AD|AD FS|
|-----|-----|-----|
|Administrator lokalny (. \Administrator)|Administrator hosta ASDK|Administrator hosta ASDK|
|AzureStack\AzureStackAdmin|Administrator hosta ASDK<br><br>Może służyć do logowania do portalu administracyjnego platformy Azure stosu<br><br>Dostępu do wyświetlania i administrować sygnałów sieci szkieletowej usług|Administrator hosta ASDK<br><br>Brak dostępu do portalu administracyjnego platformy Azure stosu<br><br>Dostępu do wyświetlania i administrować sygnałów sieci szkieletowej usług<br><br>Właściciel nie jest już z domyślny dostawca subskrypcji (punktu dystrybucji)|
|AzureStack\CloudAdmin|Można uzyskać dostęp i uruchamiania poleceń dozwolonych w ramach uprzywilejowanego punktu końcowego|Można uzyskać dostęp i uruchamiania poleceń dozwolonych w ramach uprzywilejowanego punktu końcowego<br><br>Nie można zalogować do hosta ASDK<br><br>Właściciel subskrypcji dostawcy domyślnego (punktu dystrybucji)|
|Administrator globalny usługi Azure AD|Używane podczas instalacji<br><br>Właściciel subskrypcji dostawcy domyślnego (punktu dystrybucji)|Nie dotyczy|
|

## <a name="what-tools-do-i-use-to-manage"></a>Jakie narzędzia należy używać do zarządzania?
Można użyć [Portal administratora stosu Azure](https://adminportal.local.azurestack.external) lub programu PowerShell do zarządzania Azure stosu. Najprostszym sposobem, aby dowiedzieć się z podstawowymi koncepcjami dotyczącymi jest za pośrednictwem portalu. Jeśli chcesz użyć programu PowerShell, należy zainstalować [programu PowerShell dla usługi Azure stosu](asdk-post-deploy.md#install-azure-stack-powershell) i [pobierania narzędzia Azure stosu z usługi GitHub](asdk-post-deploy.md#download-the-azure-stack-tools).

Stos Azure jako jego podstawowy mechanizm wdrażania, zarządzania i organizacji korzysta z usługi Azure Resource Manager. Jeśli użytkownik chce zarządzać stosu Azure oraz pomóc obsługi użytkowników, możesz dowiedzieć się o usługi Azure Resource Manager. Dowiedz się więcej odczytując [wprowadzenie do korzystania z usługi Azure Resource Manager oficjalny dokument](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Twoje obowiązki typowe
Użytkownicy chcą korzystać z usługi. Względem ich roli użytkownika głównego jest udostępniają te usługi do nich. Przy użyciu ASDK, możesz dowiedzieć się do oferty usługi i sposobu wprowadzania tych usług dostępnych przez [Tworzenie planów, ofertami i przydziały](asdk-offer-services.md). Należy również dodać elementy do witryny marketplace, takich jak obrazy maszyny wirtualnej. Najprostszym sposobem jest [pobieranie elementów marketplace](asdk-marketplace-item.md) z platformy Azure, Azure stosu.

> [!NOTE]
> Jeśli chcesz przetestować planów, ofertami i usług, należy użyć [portal użytkowników](https://portal.local.azurestack.external); nie [portalu administratora](https://adminportal.local.azurestack.external).

Oprócz zapewnienia usług, należy wykonać wszystkie regularne obowiązki operatora stosu Azure do zachowania ASDK działa prawidłowo. Tych zadań obejmują następujące czynności:
- Dodaj konta użytkowników dla usługi Azure Active Directory (Azure AD) lub wdrożeniach usługi Active Directory Federation Services (AD FS).
- Przypisywanie dostępu opartej na rolach ról kontroli (RBAC) (nie jest to ograniczona tylko administratorom)
- Monitor kondycji infrastruktury
- Zarządzanie zasobami sieci i magazynu
- Wymiana sprzętu komputera hosta zestawu programowanie nie powiodło się 

## <a name="where-to-get-support"></a>Gdzie można uzyskać pomoc techniczną
Dla zestawu SDK, jedyną opcją pomocy technicznej jest, aby zadać pytania dotyczące pomocy technicznej w [forum Microsoft Azure stosu](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Jeśli kliknij ikonę Pomoc i obsługa techniczna (znak zapytania) w prawym górnym rogu portalu administratora, a następnie kliknij przycisk **nowy obsługuje żądania**, to spowoduje otwarcie witryny fora bezpośrednio. Fora te są regularnie monitorowane. 

> [!IMPORTANT]
> Ponieważ ASDK środowiska do oceny, nie jest oficjalną obsługiwane oferowane za pośrednictwem usług obsługi klienta firmy Microsoft (CSS).

## <a name="next-steps"></a>Kolejne kroki
[Wdrażanie ASDK](asdk-install.md)

