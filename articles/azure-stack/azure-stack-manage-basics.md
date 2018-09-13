---
title: Podstawy administracji w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, co musisz wiedzieć, aby administrować usługi Azure Stack.
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
ms.date: 06/08/2018
ms.author: mabrigg
ms.openlocfilehash: 39dc48b1357b07932efbccaf002f54ca58edae24
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35938440"
---
# <a name="azure-stack-administration-basics"></a>Podstawy administracji w usłudze Azure Stack
Istnieje kilka kwestii, o których należy wiedzieć, jeśli jesteś nowym użytkownikiem usługi Azure Stack administracji. Niniejsze wskazówki zawiera omówienie Twojej roli jako operatorów usługi Azure Stack i co jest potrzebne do Poinformuj użytkowników dla nich, aby szybko stać się produktywności.

## <a name="understand-the-builds"></a>Zrozumienie kompilacji

### <a name="integrated-systems"></a>Systemy zintegrowane

Jeśli używasz to system zintegrowany z usługi Azure Stack zaktualizowanych wersji usługi Azure Stack są dystrybuowane za pośrednictwem pakietów aktualizacji. Można zaimportować te pakiety i zastosować je za pomocą kafelka aktualizacje w portalu administratora.
 
### <a name="development-kit"></a>Zestaw Development kit

Jeśli używasz usługi Azure Stack Development Kit, zapoznaj się z [co to jest usługa Azure Stack?](.\asdk\asdk-what-is.md) artykuł, aby upewnić się, że zrozumienie przeznaczenia deweloperski i jego pewne ograniczenia. Zestaw development kit należy używać jako "piaskownicy," gdzie możesz oceny usługi Azure Stack i opracowywać i testować aplikacje w środowisku nieprodukcyjnym. (Aby uzyskać informacje na temat wdrażania, zobacz [wdrożenia usługi Azure Stack Development Kit](.\asdk\asdk-install.md) artykułu.)

Takich jak Azure możemy szybkie innowacje. Regularnie udostępnimy nowe kompilacje. Jeśli korzystasz z deweloperski i chcesz przenieść od ostatniej kompilacji, należy najpierw [ponowne wdrażanie usługi Azure Stack](.\asdk\asdk-redeploy.md). Nie można zastosować pakietów aktualizacji. Ten proces trwa, ale ma można wypróbować najnowsze funkcje. Dokumentacji zestawu dla deweloperów w witrynie odzwierciedla najnowszej kompilacji wydania.

## <a name="learn-about-available-services"></a>Dowiedz się więcej o dostępnych usług

Potrzebna będzie świadomości z usług, które można udostępnić użytkownikom. Usługa Azure Stack obsługuje podzbiór usług platformy Azure. Lista obsługiwanych usług będą w dalszym ciągu ewolucji.

**Podstawowe usługi**

Domyślnie usługi Azure Stack obejmuje następujące "podstawowe usługi" podczas wdrażania usługi Azure Stack:

- Wystąpienia obliczeniowe
- Magazyn
- Networking
- Usługa Key Vault

W przypadku tych fundamentalnych usług możesz zaoferować Infrastructure-as-a-Service (IaaS) do użytkowników z minimalną konfiguracją.

**Usługi dodatkowe**

Obecnie Obsługujemy następujące dodatkowe usługi Platform-as-a-Service (PaaS):

- App Service
- Azure Functions
- Bazy danych SQL i MySQL

Usługi te wymagają dodatkowej konfiguracji przed można udostępnić je użytkownikom. Aby uzyskać więcej informacji zobacz "Samouczki" i "porad guides\Offer usługi" części nasza dokumentacja operatora usługi Azure Stack.

**Plan usługi**

Usługa Azure Stack będą w dalszym ciągu dodano obsługę usług platformy Azure. Przewidywany plan, można zobaczyć [usługi Azure Stack: stanowi rozszerzenie platformy Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) oficjalny dokument. Można również monitorować [wpisów w blogu usługi Azure Stack](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) dla nowych anonsów.

## <a name="what-account-should-i-use"></a>Jakiego konta mam korzystać?
Istnieje kilka istotnych kwestii konta, których należy wiedzieć podczas zarządzania usługi Azure Stack. Szczególnie w przypadku wdrożeń przy użyciu systemu Windows Server Active Directory Federation Services (AD FS) jako dostawcy tożsamości, a nie usługi Azure Active Directory (Azure AD). Obowiązują następujące zastrzeżenia konta do usługi Azure Stack zintegrowane systemy i ASDK wdrożeń:


|Konto|Azure AD|AD FS|
|-----|-----|-----|
|Administrator lokalny (. \Administrator)|Administrator hosta ASDK|Administrator hosta ASDK|
|AzureStack\AzureStackAdmin|Administrator hosta ASDK<br><br>Może służyć do logowania do portalu administracyjnego usługi Azure Stack<br><br>Dostęp do wyświetlania i administrować pierścieni usługi Service Fabric|Administrator hosta ASDK<br><br>Brak dostępu do portalu administracyjnego usługi Azure Stack<br><br>Dostęp do wyświetlania i administrować pierścieni usługi Service Fabric<br><br>Nie jest już właściciela z subskrypcji domyślny dostawca (DPS)|
|AzureStack\CloudAdmin|Można uzyskać dostęp, a następnie uruchom polecenia dozwolonych w uprzywilejowanych punktu końcowego|Można uzyskać dostęp, a następnie uruchom polecenia dozwolonych w uprzywilejowanych punktu końcowego<br><br>Nie można zalogować na hoście ASDK<br><br>Właściciel subskrypcji dostawcy domyślnego (DPS)|
|Administrator globalny usługi Azure AD|Używany podczas instalacji<br><br>Właściciel subskrypcji dostawcy domyślnego (DPS)|Nie dotyczy|
|

## <a name="what-tools-do-i-use-to-manage"></a>Jakie narzędzia należy używać do zarządzania?
 
Możesz użyć [portal administratora](azure-stack-manage-portals.md) lub programu PowerShell do zarządzania usługi Azure Stack. Najprostszym sposobem podstawowe informacje na temat jest za pośrednictwem portalu. Jeśli chcesz użyć programu PowerShell istnieją procedury przygotowywania. Należy najpierw [zainstalować](azure-stack-powershell-install.md) programu PowerShell, [Pobierz](azure-stack-powershell-download.md) dodatkowych modułów i [skonfigurować](azure-stack-powershell-configure-admin.md) programu PowerShell.

Usługa Azure Stack jako jego podstawowy mechanizm wdrażania, zarządzania i organizacji korzysta z usługi Azure Resource Manager. Jeśli użytkownik chce zarządzać usługi Azure Stack oraz pomóc je obsługi użytkowników, można dowiedzieć się o usługi Resource Manager. Zobacz [Rozpoczynanie pracy z usługą Azure Resource Manager](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) oficjalny dokument.

## <a name="your-typical-responsibilities"></a>Twoje obowiązki typowe

Użytkownicy chcą korzystać z usług. Ich względem Twojej roli głównego jest udostępnienia im tych usług. Należy zdecydować, jakie usługi do oferty i udostępnić te usługi, tworzenie planów, ofert i przydziałów. Aby uzyskać więcej informacji, zobacz [Przegląd oferty usług w usłudze Azure Stack](azure-stack-offer-services-overview.md). 

Należy także dodać elementy do [rynku](azure-stack-marketplace.md), takich jak obrazy maszyn wirtualnych. Najprostszym sposobem jest [pobieranie elementów portalu marketplace z platformy Azure do usługi Azure Stack](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Jeśli chcesz przetestować plany, oferty i usług, należy użyć [portal użytkowników](azure-stack-manage-portals.md); z portalu administratora.

Oprócz zapewniania usług, należy wykonać wszystkie regularne obowiązki operatora zapewnienie usługi Azure Stack i uruchomić. Te obowiązków należą:

- Dodaj konta użytkowników (dla [usługi Azure Active Directory](azure-stack-add-new-user-aad.md) wdrożenia lub [Active Directory Federation Services](azure-stack-add-users-adfs.md) wdrożenia)
- [Przypisywanie ról (RBAC) kontroli dostępu opartej na rolach](azure-stack-manage-permissions.md) (to nie jest ograniczony do administratorów).
- [Monitoruj kondycję infrastruktury](azure-stack-monitor-health.md)
- Zarządzanie [sieci](azure-stack-viewing-public-ip-address-consumption.md) i [magazynu](azure-stack-manage-storage-accounts.md) zasobów
- Wymiana sprzętu zły, na przykład [wymienić uszkodzony dysk](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Co mówić użytkownikom

Musisz poinformować użytkowników, sposób pracy z usługami w usłudze Azure Stack, jak połączyć się w środowisku i subskrybowanie oferty. Oprócz wszystkich dokumentów niestandardowych, możesz chcieć zapewnić swoim użytkownikom może skierować użytkowników do witryny usługi Azure Stack użytkowników dokumentacji.

**Informacje dotyczące pracy z usługami w usłudze Azure Stack**

Ma informacji, które użytkownicy muszą zrozumieć, zanim będą oni używać usług i twórz aplikacje w usłudze Azure Stack. Na przykład istnieją określone wymagania wersji programu PowerShell i interfejsu API. Ponadto istnieją pewne różnice funkcji między usługą na platformie Azure i usługę równoważne w usłudze Azure Stack. Upewnij się, że użytkownicy przejrzeć następujące artykuły:

- [Zagadnienia dotyczące klucza: przy użyciu usług lub tworzenia aplikacji dla usługi Azure Stack](user/azure-stack-considerations.md)
- [Uwagi dotyczące maszyn wirtualnych w usłudze Azure Stack](user/azure-stack-vm-considerations.md)
- [Miejsca do magazynowania: różnice i zagadnienia dotyczące](user/azure-stack-acs-differences.md)

Informacje przedstawione w tych artykułach zawiera podsumowanie różnic między usługą na platformie Azure i usługi Azure Stack. Uzupełnia on informacje, które są dostępne dla usługi platformy Azure w globalnych dokumentacji platformy Azure.

**Nawiązywanie połączenia usługi Azure Stack jako użytkownik**

W środowisku deweloperskim zestaw Jeśli użytkownik nie ma dostępu pulpitu zdalnego na hoście kit rozwoju one należy skonfigurować połączenia wirtualnej sieci prywatnej (VPN) przed uzyskaniem dostępu do usługi Azure Stack. Zobacz [nawiązać połączenie z usługi Azure Stack](azure-stack-connect-azure-stack.md). 

Użytkownicy chcą wiedzieć, jak [uzyskać dostęp do portalu użytkownika ](user/azure-stack-use-portal.md) lub sposób nawiązywania połączeń za pomocą programu PowerShell. W środowisku systemów zintegrowanych adres portalu użytkownika różni się dla wdrożenia. Należy zapewnić swoim użytkownikom poprawny adres URL.

Jeśli przy użyciu programu PowerShell, użytkownicy mogą mieć do rejestrowania dostawców zasobów, zanim rozpoczną korzystanie z usług. (Dostawca zasobów zarządza usługa. Adapterem, sieci dostawcy zasobów zarządza zasobami, takimi jak sieci wirtualnych, interfejsy sieciowe i moduły równoważenia obciążenia.) Muszą one [zainstalować](user/azure-stack-powershell-install.md) programu PowerShell, [Pobierz](user/azure-stack-powershell-download.md) dodatkowych modułów i [skonfigurować](user/azure-stack-powershell-configure-user.md) programu PowerShell (co obejmuje rejestracji dostawcy zasobów).

**Subskrybowanie oferty**

Zanim użytkownik może uzyskać dostęp do usług, muszą one [subskrybowaniu oferty](azure-stack-subscribe-plan-provision-vm.md) utworzony jako operator.

## <a name="where-to-get-support"></a>Gdzie można uzyskać pomoc techniczną

### <a name="integrated-systems"></a>Systemy zintegrowane

Zintegrowany system jest skoordynowany eskalacji i proces rozpoznawania między firmami Microsoft i partnerów sprzętowych producenta sprzętu (OEM).

Jeśli wystąpi problem services chmurze, pomoc techniczna jest oferowana za pośrednictwem usług obsługi klienta firmy Microsoft (CSS). Jeśli kliknij ikonę Pomoc i obsługa techniczna (znak zapytania) w prawym górnym rogu portalu administratora, a następnie kliknij przycisk **nowe żądanie obsługi**, spowoduje to otwarcie witryny, w którym bezpośrednio otwórz żądanie obsługi.

Jeśli występuje problem z wdrożeniem, poprawek i aktualizacji, sprzętu (w tym pola jednostki FRU) i oprogramowanie marki sprzętu, takiego jak oprogramowanie uruchomione na hoście cyklu życia sprzętu, najpierw skontaktuj się z dostawcą sprzętu OEM.

Do innych skontaktuj się z pomocą firmy Microsoft CSS.

### <a name="development-kit"></a>Zestaw Development kit

Dla zestawu SDK, możesz zadawać pytania dotyczące pomocy technicznej, w [fora firmy Microsoft](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Jeśli kliknij ikonę Pomoc i obsługa techniczna (znak zapytania) w prawym górnym rogu portalu administratora, a następnie kliknij przycisk **nowe żądanie obsługi**, otworzy się w witrynie forów bezpośrednio. Monitorowane są regularnie forów. Ponieważ zestaw development kit jest środowisko wersji ewaluacyjnej, nie jest oficjalnym obsługiwane oferowane za pośrednictwem firmy Microsoft CSS.

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie regionami w usłudze Azure Stack](azure-stack-region-management.md)


