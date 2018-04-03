---
title: Azure podstawy administracji stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, co jest potrzebne do administrowania systemem Azure stosu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mabrigg
ms.openlocfilehash: df4a5a17ad034ae5d6ab82791c020634a8758b71
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-administration-basics"></a>Azure podstawy administracji stosu
Istnieje kilka kwestii, które trzeba znać, jeśli zaczynasz administracji stosu Azure. We wskazówkach tych zawiera omówienie roli jako operator stosu Azure i należy mówić użytkownikom na ich szybko stać się produktywności.

## <a name="understand-the-builds"></a>Zrozumienie kompilacji

### <a name="integrated-systems"></a>Systemy zintegrowane

Jeśli używasz systemu Azure stosu zintegrowane zaktualizowane wersje stosu Azure są dystrybuowane za pośrednictwem pakietów aktualizacji. Można importować te pakiety i zastosować je za pomocą kafelka aktualizacje w portalu administratora.
 
### <a name="development-kit"></a>Zestaw deweloperski

Jeśli używasz usługi Azure stosu Development Kit Przejrzyj [co to jest Azure stosu?](.\asdk\asdk-what-is.md) artykuł, aby upewnić się, zrozumienie przeznaczenia zestaw deweloperski, a także jej ograniczenia. Zestaw deweloperski należy używać jako "piaskownicy," gdzie można ocenić stosu Azure i opracowanie i przetestowanie aplikacji w środowiskach nieprodukcyjnych. (Aby uzyskać informacje na temat wdrażania, zobacz [wdrożenia usługi Azure stosu Development Kit](.\asdk\asdk-deploy.md) samouczek.)

Takich jak Azure możemy innowacji szybko. Regularnie udostępnimy nowe kompilacje. Jeśli pracujesz w zestawie, i chcesz przenieść do ostatniej kompilacji, należy najpierw [ponownie wdrożyć stosu Azure](.\asdk\asdk-redeploy.md). Nie można zastosować pakietów aktualizacji. Ten proces trwa, ale korzyścią jest można wypróbować najnowszych funkcji. Dokumentacja zestawu Programowanie w naszej witrynie sieci Web odzwierciedla najnowszej kompilacji wydania.

## <a name="learn-about-available-services"></a>Dowiedz się więcej o dostępnych usług

Będziesz potrzebować pogłębianie wiedzy na temat usług, które można udostępnić użytkownikom. Stos Azure obsługuje usług platformy Azure. Lista obsługiwanych usług będzie rozwijać.

**Podstawowych usług**

Domyślnie stosu Azure zawiera następujące "podstawowych usługi" podczas wdrażania usługi Azure stosu:

- Wystąpienia obliczeniowe
- Magazyn
- Networking
- Usługa Key Vault

Z tych podstawowych usług może oferować użytkownikom z minimalną konfiguracją infrastruktury jako — usługa (IaaS).

**Dodatkowe usługi**

Obecnie obsługują następujące dodatkowe usługi platformy jako — usługa (PaaS):

- App Service
- Azure Functions
- Bazy danych SQL i MySQL

Te usługi wymagają dodatkowej konfiguracji przed można udostępnić je użytkownikom. Aby uzyskać więcej informacji zobacz "Samouczki" i "guides\Offer poradnik dotyczący usługi" części dokumentacji operator stosu Azure.

**Plan usługi**

Stos Azure będą w dalszym ciągu obsługę usług platformy Azure. Dla planowanego plan, zobacz [stosu Azure: rozszerzenie Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) oficjalny dokument. Można również monitorować [stosu Azure blogach](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) dla nowych anonsów.

## <a name="what-account-should-i-use"></a>Jakiego konta należy użyć?
Istnieje kilka uwagi dotyczące konta, które należy zwrócić uwagę podczas zarządzania Azure stosu. Szczególnie w przypadku wdrożeń za pomocą systemu Windows Server Active Directory Federation Services (AD FS) jako dostawca tożsamości, a nie usługi Azure Active Directory (Azure AD). Następujące uwagi dotyczące konta dotyczą zarówno stosu Azure zintegrowanych systemów i ASDK wdrożenia:


|Konto|Azure AD|AD FS|
|-----|-----|-----|
|Administrator lokalny (. \Administrator)|Administrator hosta ASDK|Administrator hosta ASDK|
|AzureStack\AzureStackAdmin|Administrator hosta ASDK<br><br>Może służyć do logowania do portalu administracyjnego platformy Azure stosu<br><br>Dostępu do wyświetlania i administrować sygnałów sieci szkieletowej usług|Administrator hosta ASDK<br><br>Brak dostępu do portalu administracyjnego platformy Azure stosu<br><br>Dostępu do wyświetlania i administrować sygnałów sieci szkieletowej usług<br><br>Właściciel nie jest już z domyślny dostawca subskrypcji (punktu dystrybucji)|
|AzureStack\CloudAdmin|Można uzyskać dostęp i uruchamiania poleceń dozwolonych w ramach uprzywilejowanego punktu końcowego|Można uzyskać dostęp i uruchamiania poleceń dozwolonych w ramach uprzywilejowanego punktu końcowego<br><br>Nie można zalogować do hosta ASDK<br><br>Właściciel subskrypcji dostawcy domyślnego (punktu dystrybucji)|
|Administrator globalny usługi Azure AD|Używane podczas instalacji<br><br>Właściciel subskrypcji dostawcy domyślnego (punktu dystrybucji)|Nie dotyczy|
|

## <a name="what-tools-do-i-use-to-manage"></a>Jakie narzędzia należy używać do zarządzania?
 
Można użyć [portalu administratora](azure-stack-manage-portals.md) lub programu PowerShell do zarządzania Azure stosu. Najprostszym sposobem, aby dowiedzieć się z podstawowymi koncepcjami dotyczącymi jest za pośrednictwem portalu. Jeśli chcesz użyć programu PowerShell istnieją kroki przygotowania. Należy [zainstalować](azure-stack-powershell-install.md) programu PowerShell, [Pobierz](azure-stack-powershell-download.md) dodatkowych modułów i [skonfigurować](azure-stack-powershell-configure-admin.md) środowiska PowerShell.

Stos Azure jako jego podstawowy mechanizm wdrażania, zarządzania i organizacji korzysta z usługi Azure Resource Manager. Jeśli użytkownik chce zarządzać stosu Azure oraz pomóc obsługi użytkowników, możesz dowiedzieć się o Menedżera zasobów. Zobacz [wprowadzenie do korzystania z usługi Azure Resource Manager](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) oficjalny dokument.

## <a name="your-typical-responsibilities"></a>Twoje obowiązki typowe

Użytkownicy chcą korzystać z usługi. Względem ich roli użytkownika głównego jest udostępniają te usługi do nich. Zdecyduj, które usługi oferowanie, a udostępnić przez utworzenie planów, ofertami i przydziały tych usług. Aby uzyskać więcej informacji, zobacz [Przegląd oferty usług Azure stosu](azure-stack-offer-services-overview.md). 

Należy również dodać elementy do [witryny marketplace](azure-stack-marketplace.md), takich jak obrazy maszyny wirtualnej. Najprostszym sposobem jest [pobieranie elementów marketplace z platformy Azure do stosu Azure](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Jeśli chcesz przetestować planów, ofertami i usług, należy użyć [portal użytkowników](azure-stack-manage-portals.md); nie portalu administratora.

Oprócz zapewnienia usług, należy wykonać wszystkie regularne obowiązki operatora przechowywania stosu Azure działa prawidłowo. Obejmują następujące obowiązki:

- Dodaj konta użytkowników (dla [usługi Azure Active Directory](azure-stack-add-new-user-aad.md) wdrożenia lub [Active Directory Federation Services](azure-stack-add-users-adfs.md) wdrożenia)
- [Przypisywanie ról (RBAC) kontroli dostępu opartej na rolach](azure-stack-manage-permissions.md) (to nie jest ograniczone do administratorów.)
- [Monitor kondycji infrastruktury](azure-stack-monitor-health.md)
- Zarządzanie [sieci](azure-stack-viewing-public-ip-address-consumption.md) i [magazynu](azure-stack-manage-storage-accounts.md) zasobów
- Zastąp zły sprzętu, na przykład [wymienić uszkodzony dysk](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Co mówić użytkownikom

Musisz poinformować użytkowników, jak pracować z usług Azure stosu, sposób nawiązywania połączenia ze środowiskiem i subskrybowanie oferty. Oprócz dokumentację niestandardowych czy może chcesz zapewnić użytkownikom, należy przekierować użytkowników do witryny dokumentacji użytkowników stosu platformy Azure.

**Zrozumienie sposobu pracy z usługami Azure stosu**

Ma informacji, które użytkownicy muszą zrozumieć przed korzystania z usług i tworzyć aplikacje w stosie Azure. Na przykład są określone wymagania wersji środowiska PowerShell i interfejs API. Są także niektóre delty funkcji między usługi na platformie Azure i odpowiednik w stosie Azure. Upewnij się, że użytkownicy następujące artykuły:

- [Kluczowe zagadnienia dotyczące: za pomocą usług lub tworzenia aplikacji dla platformy Azure stosu](user/azure-stack-considerations.md)
- [Zagadnienia dotyczące maszyn wirtualnych Azure stosu](user/azure-stack-vm-considerations.md)
- [Magazynu: różnice i zagadnienia dotyczące](user/azure-stack-acs-differences.md)

Informacje zawarte w następujących artykułach zawiera podsumowanie różnic między usługą platformy Azure i stosu Azure. Uzupełnia on informacje, które są dostępne dla usługi Azure w globalnej dokumentacji platformy Azure.

**Połącz stos Azure jako użytkownik**

W środowisku development kit Jeśli użytkownik nie ma dostępu pulpitu zdalnego na hoście zestawu programowanie one należy skonfigurować połączenie wirtualnej sieci prywatnej (VPN) przed uzyskaniem dostępu do stosu Azure. Zobacz [nawiązania połączenia platformy Azure stosu](azure-stack-connect-azure-stack.md). 

Użytkownicy będą chcieli wiedzieć, jak [dostępu do portalu użytkownika ](user/azure-stack-use-portal.md) lub sposób nawiązywania połączenia za pomocą programu PowerShell. W środowisku zintegrowanych systemów adres portalu użytkownika różni się dla wdrożenia. Należy zapewnić użytkownikom poprawny adres URL.

Jeśli przy użyciu programu PowerShell, użytkownicy mogą mieć można zarejestrować dostawcy zasobów, aby móc korzystać z usługi. (Dostawca zasobów zarządza usługi. For example, sieci dostawcy zasobów zarządza zasoby, takie jak sieci wirtualnych, interfejsów sieciowych i moduły równoważenia obciążenia.) Muszą one [zainstalować](user/azure-stack-powershell-install.md) programu PowerShell, [Pobierz](user/azure-stack-powershell-download.md) dodatkowych modułów i [skonfigurować](user/azure-stack-powershell-configure-user.md) środowiska PowerShell (w tym Rejestracja dostawcy zasobów).

**Subskrybowanie oferty**

Zanim użytkownik może uzyskać dostęp do usług, muszą one [subskrybować ofertę](azure-stack-subscribe-plan-provision-vm.md) utworzony jako operatora.

## <a name="where-to-get-support"></a>Gdzie można uzyskać pomoc techniczną

### <a name="integrated-systems"></a>Systemy zintegrowane

Zintegrowany system jest w skoordynowany sposób eskalacją i procesu rozpoznawania między firmą Microsoft a partnerskimi dostawcami sprzętu producenta sprzętu (OEM).

Jeśli wystąpi problem usługi w chmurze, pomocy technicznej jest oferowana za pośrednictwem usług obsługi klienta firmy Microsoft (CSS). Jeśli kliknij ikonę Pomoc i obsługa techniczna (znak zapytania) w prawym górnym rogu portalu administratora, a następnie kliknij przycisk **nowy obsługuje żądania**, spowoduje to otwarcie witryny, w której bezpośrednio otworzyć żądania obsługi.

Jeśli wystąpi problem z wdrożeniem, poprawek i aktualizacji, sprzętu (w tym polu jednostki FRU) i oprogramowanie marki sprzętu, takiego jak oprogramowanie uruchomiona na hoście cyklu życia sprzętu, skontaktuj się z dostawcą sprzętu OEM najpierw.

Dla żadnych innych czynności skontaktuj się z Microsoft CSS.

### <a name="development-kit"></a>Zestaw deweloperski

Dla zestawu SDK, można zadawać pytania dotyczące pomocy technicznej w [fora Microsoft](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Jeśli kliknij ikonę Pomoc i obsługa techniczna (znak zapytania) w prawym górnym rogu portalu administratora, a następnie kliknij przycisk **nowy obsługuje żądania**, to spowoduje otwarcie witryny fora bezpośrednio. Fora te są regularnie monitorowane. Ponieważ zestaw deweloperski środowiska do oceny, nie jest oficjalną obsługiwane oferowane przez firmy Microsoft CSS.

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie regionu Azure stosu](azure-stack-region-management.md)


