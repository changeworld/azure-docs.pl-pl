---
title: Ustawienia i dane mobilne — często zadawane pytania | Dokumentacja firmy Microsoft
description: Zawiera odpowiedzi na pytania Administratorzy IT mogą mieć o ustawieniach i synchronizacji danych aplikacji.
services: active-directory
keywords: Enterprise mobilnego ustawienia stanu, chmury systemu windows, często zadawane pytania na roamingu stanu przedsiębiorstwa
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: c0824f5c-129b-4240-969f-921f6a64eae7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: markvi
ms.openlocfilehash: f33376d5f68d64495a7a90e62870f3ec14f73246
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258718"
---
# <a name="settings-and-data-roaming-faq"></a>Roaming ustawień i danych — często zadawane pytania
Ten artykuł zawiera odpowiedzi na pytania Administratorzy IT mogą mieć o ustawieniach i synchronizacji danych aplikacji.

## <a name="what-data-roams"></a>Jakie dane uzyskuje mobilny dostęp?
**Ustawienia systemu Windows**: ustawienia komputera, które są wbudowane w system operacyjny Windows. Ogólnie rzecz biorąc ustawienia, które personalizowanie komputera i zawierają następujące kategorie:

* *Motyw*, która obejmuje funkcje, takie jak ustawienia motywu i paska zadań pulpitu.
* *Ustawienia programu Internet Explorer*, w tym ostatnio otwartą kart i Ulubione.
* *Ustawienia przeglądarki krawędzi*, na przykład Ulubione i odczytu listy.
* *Hasła*, w tym hasła internetowe, profile sieci Wi-Fi i inne.
* *Preferencje językowe*, w tym ustawienia układów klawiatury, język systemu, Data i czas i więcej.
* *Łatwość dostępu do funkcji*, na przykład motywu dużego kontrastu, Narrator i Lupa.
* *Inne ustawienia systemu Windows*, takie jak ustawienia wiersza polecenia i listy aplikacji.

**Dane aplikacji**: aplikacji uniwersalnych systemu Windows można zapisać ustawienia danych do folderu mobilnego, a wszystkie dane zapisane w tym folderze zostaną automatycznie zsynchronizowane. To developer poszczególnych aplikacji do projektowania aplikacji, aby skorzystać z zalet tej możliwości. Aby uzyskać więcej informacji dotyczących sposobu tworzenia aplikacji uniwersalnych systemu Windows, który korzysta z roamingu, zobacz [interfejsu API magazynu appdata](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) i [appdata systemu Windows 8 roamingu blog deweloperów](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Jakie konto jest używane do synchronizacji ustawień?
W systemie Windows 8 i Windows 8.1 ustawienia synchronizacji zawsze używany kont Microsoft konsumenta. Przedsiębiorstwa użytkownicy mają możliwość łączenia z konta Microsoft do konta domeny usługi Active Directory do uzyskania dostępu do ustawień synchronizacji. W systemie Windows 10 to połączone konto Microsoft, że funkcja jest zastępowany framework podstawowego i pomocniczego konta.

Nie zdefiniowano podstawowe konto jako konto używane do logowania do systemu Windows. Może to być konto Microsoft, konto usługi Azure Active Directory (Azure AD), konta lokalne usługi Active Directory lub konta lokalnego. Oprócz kontem podstawowym użytkownicy systemu Windows 10 można dodać jeden lub więcej kont dodatkowej chmury dla swojego urządzenia. Drugie konto jest zazwyczaj kontem Microsoft, konta usługi Azure AD lub niektóre inne konto, takie jak Gmail lub Facebook. Te konta dodatkowej zapewniają dostęp do dodatkowych usług, takich jak logowanie jednokrotne i Sklepu Windows, ale nie są one w stanie synchronizacji ustawień zasilania.

W systemie Windows 10, kontem podstawowym urządzenia może służyć do celów synchronizacji ustawień (zobacz [jak uaktualnić synchronizację ustawień konta Microsoft w systemie Windows 8 do usługi Azure AD synchronizacja ustawień w systemie Windows 10?](active-directory-windows-enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Dane nigdy nie jest mieszana między kontami innego użytkownika na urządzeniu. Istnieją dwie reguły synchronizacji ustawień:

* Ustawienia systemu Windows będzie zawsze są przekazywane z kontem podstawowym.
* Dane aplikacji zostaną oznaczone konto używane do uzyskania dostępu aplikacji. Tylko aplikacje z kontem podstawowym zostanie zsynchronizowany. Znakowanie własność aplikacji jest określana, gdy aplikacja jest ładowana za pośrednictwem Sklepu Windows lub zarządzania urządzeniami przenośnymi (MDM).

Jeśli nie można zidentyfikować właściciela aplikacji, będą są przekazywane z kontem podstawowym. Jeśli urządzenie zostanie uaktualniony z systemu Windows 8 lub Windows 8.1 do systemu Windows 10, wszystkie aplikacje będą oznakowane jako uzyskaną przez konta Microsoft. To dlatego większość użytkowników uzyskać aplikacje Sklepu Windows i nie było żadnych obsługę Sklepu Windows dla konta usługi Azure AD przed systemem Windows 10. Jeśli aplikacja zostanie zainstalowana przy użyciu licencję w trybie offline, aplikacja zostanie oznaczone przy użyciu konta podstawowego na urządzeniu.

> [!NOTE]
> Urządzenia z systemem Windows 10 należących do przedsiębiorstwa i są połączone z usługą Azure AD można już połączyć ich kont Microsoft na konto domeny. Możliwość połączenia konta Microsoft na konto domeny i mieć synchronizacji danych wszystkie użytkownika do konta Microsoft (to znaczy, że konto Microsoft mobilnych za pośrednictwem połączenia konta Microsoft i funkcjonalności usługi Active Directory) jest usuwany z urządzeń z systemem Windows 10, które dołączyły do połączonych środowiska usługi Active Directory lub Azure AD.
>
>

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Jak uaktualnić synchronizację ustawień konta Microsoft w systemie Windows 8 do usługi Azure AD sync ustawienia w systemie Windows 10?
Jeśli użytkownik są przyłączone do domeny usługi Active Directory, systemem Windows 8 lub Windows 8.1 połączone konto Microsoft, będzie synchronizować ustawienia za pomocą konta Microsoft. Po uaktualnieniu do systemu Windows 10, będą nadal synchronizują ustawienia użytkownika za pomocą konta Microsoft, jak długo użytkownik jest użytkownikiem domeny i domeny usługi Active Directory nie łączy się z usługą Azure AD.

Jeśli lokalnej domeny usługi Active Directory połączyć się z usługą Azure AD, urządzenie będzie podejmować próby synchronizacji ustawień za pomocą połączenia konta usługi Azure AD. Jeśli administrator usługi Azure AD nie obsługuje roamingu stanu przedsiębiorstwa, Twoje połączone konta usługi Azure AD przestanie Synchronizowanie ustawień. Jeśli użytkownik jest użytkownikiem systemu Windows 10, a następnie zaloguj się przy użyciu tożsamości usługi Azure AD, rozpocznie synchronizację ustawień systemu windows, jak administrator włącza ustawienia synchronizacji za pomocą usługi Azure AD.

Jeśli wszystkie dane osobowe są przechowywane na urządzeniu firmowych, należy pamiętać, że systemu operacyjnego i danych aplikacji rozpocznie synchronizację z usługą Azure AD. To ma następujące konsekwencje:

* Ustawienia osobiste konto Microsoft będzie rozchodzenia oprócz ustawienia w pracy lub szkołą konta usługi Azure AD. Jest to spowodowane konta Microsoft i ustawień usługi Azure AD synchronizowane są teraz przy użyciu osobnych kont.
* Dane osobowe, takie jak hasła sieci Wi-Fi, sieci web, poświadczeń i Ulubione programu Internet Explorer, które wcześniej zostały zsynchronizowane przez połączone konto Microsoft zostaną zsynchronizowane za pomocą usługi Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Jak konta Microsoft i pracy współdziałanie Roaming stanu przedsiębiorstwa usług Azure AD?
W wersjach listopada 2015 lub nowszego systemu Windows 10 roamingu stanu przedsiębiorstwa jest obsługiwana tylko dla jednego konta w czasie. Zaloguj się do systemu Windows za pomocą służbowego lub szkolnego konta usługi Azure AD, wszystkie dane zostaną zsynchronizowane za pomocą usługi Azure AD. Jeśli logujesz się do systemu Windows za pomocą osobistego konta Microsoft, wszystkie dane zostaną zsynchronizowane za pomocą konta Microsoft. Uniwersalnych appdata będzie są przenoszone przy użyciu tylko podstawowy logowania konta na urządzenie i będzie są przekazywane tylko wtedy, gdy licencji aplikacji jest własnością podstawowego konta. Appdata uniwersalnych aplikacji właścicielem żadnych kont nie będą synchronizowane.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Czy ustawienia synchronizacji dla konta usługi Azure AD z wieloma dzierżawcami?
Kiedy usługi Azure AD wielu kont z różnych dzierżaw usługi Azure AD znajdują się na tym samym urządzeniu, musisz zaktualizować rejestr urządzenia w celu komunikowania się z usługą Azure Rights Management dla każdego dzierżawcy usługi Azure AD.  

1. Znajdź identyfikator GUID dla każdego dzierżawcy usługi Azure AD. Otwórz Azure portal i wybierz dzierżawa usługi Azure AD. Identyfikator GUID dzierżawy znajdują się na stronie właściwości dla wybranej dzierżawy (https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties), oznaczone **identyfikator katalogu**. 
2. Po utworzeniu identyfikatora GUID, konieczne będzie dodanie klucza rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<dzierżawy Identyfikatora GUID >**.
   Z **dzierżawy Identyfikatora GUID** kluczy, Utwórz nową wartość ciągu wielokrotnego (REG-MULTI-SZ) o nazwie **AllowedRMSServerUrls**. Danych określ licencjonowania punktu dystrybucji, adresy URL innych dzierżawców Azure, które uzyskuje dostęp do urządzenia.
3. Adresy URL licencjonowania punktu dystrybucji można znaleźć, uruchamiając **Get-AadrmConfiguration** polecenia cmdlet w AADRM module. Jeśli wartości **LicensingIntranetDistributionPointUrl** i **LicensingExtranetDistributionPointUrl** są różne, określ obie wartości. Jeśli wartości są takie same, określ wartość tylko raz.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Jakie są opcje Ustawienia roamingu, istniejących aplikacji klasycznych systemu Windows?
Roaming działa tylko dla aplikacji uniwersalnych systemu Windows. Dostępne są dwie opcje do włączania mobilnego na istniejącą aplikację pulpitu systemu Windows:

* [Mostek pulpitu](https://aka.ms/desktopbridge) pomaga Przełącz istniejących aplikacji klasycznych systemu Windows dla platformy uniwersalnej systemu Windows. W tym miejscu minimalnych zmianach w kodzie będą musieli korzystać z mobilne dane aplikacji Azure AD. Mostek pulpitu udostępnia aplikacjami za pomocą tożsamości aplikacji, co jest wymagane do włączenia dane aplikacji mobilnych dla istniejącej aplikacji klasycznych.
* [Wirtualizacji środowiska użytkownika (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) pomaga utworzyć ustawienia niestandardowe szablon dla istniejącej aplikacji klasycznych systemu Windows i włączyć mobilnych dla aplikacji Win32. Ta opcja nie wymaga Deweloper aplikacji zmienić kod aplikacji. UE-V jest ograniczona do lokalnej usługi Active Directory mobilnego dla klientów, którzy kupili pakietu Microsoft Desktop Optimization Pack.

Administratorzy mogą skonfigurować wirtualizacji środowiska użytkownika są przekazywane dane aplikacji pulpitu systemu Windows, zmieniając roaming ustawień systemu operacyjnego i danych aplikacji uniwersalnej za pośrednictwem [wirtualizacji środowiska użytkownika, zasady grupy](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), takie jak:

* Są przekazywane zasad grupy ustawienia systemu Windows
* Nie Synchronizuj zasad grupy aplikacji systemu Windows
* Roaming w sekcji aplikacji w programie Internet Explorer

Microsoft może w przyszłości, zbadaj sposoby UE-V, ściśle zintegrowane z systemem Windows i rozszerzyć UE-V z roamingu ustawień za pomocą usługi Azure AD cloud.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Lokalnie może przechowywać zsynchronizowane ustawienia i dane?
Roamingu stanu przedsiębiorstwa przechowuje wszystkie zsynchronizowane dane w chmurze Azure. UE-V oferuje lokalnego roamingu rozwiązania.

## <a name="who-owns-the-data-thats-being-roamed"></a>Kto jest właścicielem danych, który jest są przekazywane?
Przedsiębiorstw własne dane są przekazywane za pośrednictwem roamingu stanu przedsiębiorstwa. Dane są przechowywane w centrum danych Azure. Wszystkie dane użytkownika są szyfrowane, zarówno przesyłanych i przechowywanych w chmurze przy użyciu usługi Azure Rights Management z usługi Azure Information Protection. Jest to ulepszenia w porównaniu do synchronizacji ustawień opartych na konto Microsoft, który szyfruje tylko niektórych poufnych danych, takich jak poświadczeń użytkownika przed opuszczeniem urządzenia.

Firma Microsoft dokłada starań w celu ochrony danych klienta. Dane ustawienia użytkownika w organizacji jest automatycznie szyfrowane przez usługę Azure Rights Management przed opuszczeniem urządzenia z systemem Windows 10, więc żaden inny użytkownik może odczytywać te dane. Jeśli Twoja organizacja ma płatna subskrypcja usługi Azure Rights Management, można korzystanie z innych funkcji ochrony, takie jak śledzenie i odwoływanie dokumentów, automatycznie chronić wiadomości e-mail zawierające poufne informacje i zarządzanie własnych kluczy ("bring własny klucz"rozwiązanie, znanej także jako BYOK). Aby uzyskać więcej informacji o tych funkcjach i jak działa ta usługa ochrony, zobacz [co to jest usługa Azure Rights Management](https://docs.microsoft.com/azure/information-protection/understand-explore/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Można zarządzać synchronizacji dla określonej aplikacji lub ustawienie?
W systemie Windows 10 nie ma nie ustawienie zarządzania urządzeniami Przenośnymi lub zasad grupy, aby wyłączyć mobilnego dla poszczególnych aplikacji. Administratorzy dzierżawy mogą wyłączyć appdata synchronizacji dla wszystkich aplikacji na urządzeniach zarządzanych, ale nie nie bardziej precyzyjną kontrolę na poziomie aplikacji lub w aplikacji.

## <a name="how-can-i-enable-or-disable-roaming"></a>Jak włączyć lub wyłączyć mobilnych?
W **ustawienia** aplikacji, przejdź do **kont** > **Synchronizuj swoje ustawienia**. Na tej stronie mogą zobaczyć konto, które jest używane do ustawienia roamingu, a można włączyć lub wyłączyć poszczególne grupy ustawień ma być przekazywane.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Co to jest zalecenia firmy Microsoft umożliwiających roaming w systemie Windows 10?
Firma Microsoft udostępnia kilka różnych ustawień roamingu rozwiązań, łącznie z profilów użytkowników mobilnych, UE-V i roamingu stanu przedsiębiorstwa.  Firma Microsoft dokłada starań, aby inwestycji w przedsiębiorstwie stanu Roaming w przyszłych wersjach systemu Windows. Jeśli Twoja organizacja nie jest gotowy lub doświadczenia z przenoszenie danych do chmury, następnie firma Microsoft zaleca użycie wirtualizacji środowiska użytkownika jako podstawowy technologii mobilnych. Jeżeli organizacja wymaga roaming obsługę istniejących aplikacji pulpitu systemu Windows, ale chcesz przenieść do chmury, firma Microsoft zaleca korzystanie z roamingu stanu przedsiębiorstwa i wirtualizacji środowiska użytkownika. Mimo że UE-V i roamingu stanu przedsiębiorstwa są bardzo podobne technologie, nie są one wykluczają się wzajemnie. One uzupełniają pomaga zapewnić, że organizacji świadczy usługi mobilnego potrzebnych użytkownikom.  

Korzystając z roamingu stanu przedsiębiorstwa i wirtualizacji środowiska użytkownika, obowiązują następujące reguły:

* Roamingu stanu przedsiębiorstwa jest podstawowym agenta roamingu na urządzeniu. UE-V jest używana do uzupełnienia "przerwa Win32".
* Powinno zostać wyłączone wirtualizacji środowiska użytkownika mobilnego dla ustawienia systemu Windows i nowoczesne danych aplikacji platformy uniwersalnej systemu Windows, gdy zasady grupy wirtualizacji środowiska użytkownika. Te są już objęte roamingu stanu przedsiębiorstwa.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>W jaki sposób roamingu stanu przedsiębiorstwa obsługuje infrastrukturę pulpitu wirtualnego (VDI)?
Roamingu stanu przedsiębiorstwa jest obsługiwana na kliencie systemu Windows 10 jednostki SKU, ale nie na serwerze jednostki SKU. Jeśli klient maszyna wirtualna znajduje się na komputerze funkcji hypervisor i zdalnego logowania się do maszyny wirtualnej, dane będą są przekazywane. Jeśli wielu użytkowników udostępnianie tego samego systemu operacyjnego i zdalnego logowania do serwera w celu obsługi całego pulpitu, roamingu może nie działać. Ostatnie scenariuszy opartych na sesji nie jest oficjalnie obsługiwana.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Co się stanie, jeśli Moja organizacja kupuje subskrypcję obejmuje usługę Azure Rights Management po zakończeniu korzystania z roamingu?
Jeśli organizacja już korzysta z roamingu w systemie Windows 10 z bezpłatnej subskrypcji usługi Azure Rights Management ograniczonej, zakupu [płatnej subskrypcji](https://azure.microsoft.com/pricing/details/information-protection/) zawierającą usługi ochrony nie będzie miał usługi Azure Rights Management ich wpływ na działanie funkcji mobilnych i bez zmian konfiguracji może być wymagane przez administratora IT.

## <a name="known-issues"></a>Znane problemy
Można znaleźć w dokumentacji w [Rozwiązywanie problemów z](active-directory-windows-enterprise-state-roaming-troubleshooting.md) sekcja zawiera listę znanych problemów. 

## <a name="related-topics"></a>Powiązane tematy
* [Przegląd roamingu stanu przedsiębiorstwa](active-directory-windows-enterprise-state-roaming-overview.md)
* [Włącz roaming stanu przedsiębiorstwa w usłudze Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Zasady grupy i ustawienia zarządzania urządzeniami Przenośnymi dla ustawień synchronizacji](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Informacje dotyczące ustawień roamingu systemu Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Rozwiązywanie problemów](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
