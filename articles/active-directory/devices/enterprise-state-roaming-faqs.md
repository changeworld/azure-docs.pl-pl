---
title: Często zadawane pytania dotyczące roaming ustawień i danych | Dokumentacja firmy Microsoft
description: Zawiera odpowiedzi na pytania administratorów IT może być o ustawieniach i synchronizowanie danych aplikacji.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f9270aff6bc2aab7e210716ffe3e21efb07b8ed
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481951"
---
# <a name="settings-and-data-roaming-faq"></a>Roaming ustawień i danych — często zadawane pytania
Ten artykuł zawiera odpowiedzi na kilka pytań, na które Administratorzy IT mogą się pojawić w ustawień i synchronizacji danych w aplikacji.

## <a name="what-data-roams"></a>Jakich danych uzyskuje mobilny dostęp?
**Ustawienia Windows**: ustawienia komputera, które są wbudowane w system operacyjny Windows. Ogólnie rzecz biorąc są to ustawienia, które personalizowanie komputera użytkownika i obejmują one następujące ogólne kategorie:

* *Motyw*, która obejmuje funkcje, takie jak ustawienia motywu i paska zadań pulpitu.
* *Ustawienia programu Internet Explorer*, włącznie z ostatnio otwartych karty i Ulubione.
* *Ustawienia przeglądarki Microsoft Edge*, na przykład Ulubione i przeczytania.
* *Hasła*, w tym hasła internetowe, profile sieci Wi-Fi i inne.
* *Preferencje językowe*, który zawiera ustawienia układów klawiatury, językiem, daty i godziny oraz więcej.
* *Łatwość dostępu do funkcji*, takich jak motyw o wysokim kontraście i Narrator, Lupa.
* *Inne ustawienia Windows*, takie jak ustawienia myszy.

**Dane aplikacji**: Uniwersalne aplikacje Windows mogła zapisywać dane ustawienia, do folderu mobilnego, a wszystkie dane zapisywane do tego folderu zostaną automatycznie zsynchronizowane. To Ty deweloperem poszczególnych aplikacji, aby zaprojektować aplikację, aby skorzystać z tej możliwości. Aby uzyskać więcej informacji na temat programowania aplikacji uniwersalnych Windows, która korzysta z roamingu, zobacz [interfejsu API usługi storage appdata](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) i [systemu Windows 8 appdata roaming blog deweloperów](https://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Które konto jest używane do celów synchronizacji ustawień?
W Windows 8.1 ustawienia synchronizacji zawsze używane kont Microsoft konsumenta. Użytkownicy korporacyjni miał możliwość łączenia z konta Microsoft do swojego konta domeny usługi Active Directory do uzyskania dostępu do ustawień synchronizacji. W systemie Windows 10 to połączone konta Microsoft, że funkcja została zastąpiona framework konta podstawowy/dodatkowy.

Konto podstawowy jest zdefiniowany jako konto używane do logowania się na Windows. Może to być konto Microsoft, konta usługi Azure Active Directory (Azure AD), konto usługi Active Directory w środowisku lokalnym lub kontem lokalnym. Oprócz podstawowego konta użytkowników systemu Windows 10 można dodać co najmniej jedno konto dodatkowej chmury na urządzenie. Drugie konto jest zazwyczaj kontem Microsoft, konta usługi Azure AD lub niektóre inne konto, takiego jak Gmail lub Facebook. Te konta pomocnicze zapewniają dostęp do dodatkowych usług, takich jak logowanie jednokrotne i Store Windows, ale nie są one w stanie zasilania, ustawienia synchronizacji.

W systemie Windows 10, konto podstawowe dla tego urządzenia może służyć do synchronizacji ustawień (zobacz [jak wykonać uaktualnienie z synchronizacja ustawień konta Microsoft w systemie Windows 8 do usługi Azure AD synchronizacja ustawień w systemie Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Dane nigdy nie jest mieszane między kontami innego użytkownika na urządzeniu. Istnieją dwie reguły synchronizacji ustawień:

* Windows ustawienia "wędrują" zawsze przy użyciu podstawowego konta.
* Dane aplikacji są oznaczane za pomocą konta używanego do nabywania aplikacji. Tylko aplikacje oznaczone przy użyciu podstawowego konta zostanie zsynchronizowany. Znakowanie własność aplikacji jest określana w przypadku aplikacji ładowanych za pośrednictwem Windows Store lub zarządzania urządzeniami przenośnymi (MDM).

Jeśli nie można zidentyfikować właściciela aplikacji, jej "wędrują" z kontem podstawowym. Jeśli urządzenie zostanie uaktualniony z systemu Windows 8 lub Windows 8.1 do systemu Windows 10, wszystkie aplikacje są oznaczane jako uzyskanych przy użyciu konta Microsoft. Jest to spowodowane większość użytkowników nabywania aplikacji za pośrednictwem Windows Store i wystąpił brak obsługi Windows Store dla konta usługi Azure AD przed systemem Windows 10. Jeśli aplikacja zostanie zainstalowana za pomocą licencji offline, aplikacja zostanie oznaczone przy użyciu podstawowego konta na urządzeniu.

> [!NOTE]
> Urządzenia z systemem Windows 10, są należących do przedsiębiorstwa, które są połączone z usługą Azure AD można już połączyć swoje konta Microsoft do konta domeny. Łączenie konta Microsoft z kontem domeny i mają synchronizacji danych wszystkich użytkowników do konta Microsoft (czyli konto Microsoft mobilnych za pomocą połączonego konta Microsoft i funkcjonalności usługi Active Directory) zostanie usunięty z systemu Windows 10 urządzenia, które są przyłączone do połączone środowisko usługi Active Directory lub Azure AD.
>
>

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Jak uaktualnić z synchronizacja ustawień konta Microsoft w systemie Windows 8 do usługi Azure AD synchronizacja ustawień w systemie Windows 10?
Jeśli użytkownik są przyłączone do domeny usługi Active Directory z systemem Windows 8.1 za pomocą połączonego konta Microsoft, zsynchronizuje ustawienia za pomocą konta Microsoft. Po uaktualnieniu do systemu Windows 10, będziesz synchronizować ustawienia użytkownika za pomocą konta Microsoft, tak długo, jak są przyłączone do domeny użytkownika i domeną usługi Active Directory nie łączy się z usługą Azure AD.

Jeśli lokalnej domeny usługi Active Directory łączą się z usługą Azure AD, urządzenie podejmie próbę synchronizacji ustawień za pomocą połączonym koncie usługi Azure AD. Jeśli administrator usługi Azure AD nie zostanie włączony Roaming stanu dla przedsiębiorstw, usługi połączone konto usługi Azure AD zostanie zatrzymane, synchronizowanie ustawień. Jeśli jesteś użytkownikiem systemu Windows 10 i zaloguj się przy użyciu tożsamości usługi Azure AD, możesz rozpocząć, synchronizowanie ustawień systemu windows, tak szybko, jak administrator włącza synchronizację ustawień za pomocą usługi Azure AD.

Jeśli jakiekolwiek dane osobowe są przechowywane na urządzeniu z systemem firmowych, należy pamiętać, że systemu operacyjnego Windows i danych aplikacji rozpocznie się synchronizacja z usługą Azure AD. Ma to następujące skutki:

* Ustawienia osobiste konta Microsoft zostanie odstępstw niezależnie od ustawienia na pracy lub szkołą kont usługi Azure AD. Jest to spowodowane synchronizowanie konta Microsoft i ustawienia usługi Azure AD są teraz przy użyciu osobnych kont.
* Dane osobowe, takie jak hasła sieci Wi-Fi, sieci web, poświadczeń i Ulubione programu Internet Explorer, które wcześniej zostały zsynchronizowane za pośrednictwem połączonego konta Microsoft zostaną zsynchronizowane za pomocą usługi Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>W jaki sposób konto Microsoft i Roaming stanu przedsiębiorstwa AD Azure współdziałania pracę?
W listopadzie 2015 lub nowszej wersje systemu Windows 10 Roaming stanu dla przedsiębiorstw jest obsługiwana tylko dla jednego konta w danym momencie. Zaloguj się do Windows przy użyciu służbowego lub szkolnego konta usługi Azure AD, wszystkie dane zostaną zsynchronizowane za pomocą usługi Azure AD. Jeśli możesz zalogować się do Windows przy użyciu osobistego konta Microsoft, wszystkie dane zostaną zsynchronizowane za pomocą konta Microsoft. Appdata uniwersalne "wędrują" przy użyciu tylko podstawowego logowania konta na urządzeniu, a jej "wędrują" tylko wtedy, gdy licencji aplikacji jest własnością podstawowego konta. Uniwersalne appdata na aplikacje należące do żadnych dodatkowych kont, nie zostaną zsynchronizowane.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Czy ustawienia synchronizacji dla kont usługi Azure AD z wieloma dzierżawcami?
W przypadku wielu usługi Azure AD konta za pośrednictwem różnych dzierżaw usługi Azure AD znajdują się na tym samym urządzeniu, należy zaktualizować rejestr urządzenia w celu komunikowania się z usługą Azure Rights Management dla każdej dzierżawy usługi Azure AD.  

1. Znajdź identyfikator GUID dla każdego dzierżawcy usługi Azure AD. Otwórz witrynę Azure portal i wybierz dzierżawę usługi Azure AD. Identyfikator GUID dla dzierżawy znajduje się na stronie właściwości dla wybranej dzierżawy (https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties), oznaczone **identyfikator katalogu**. 
2. Po utworzeniu identyfikatora GUID, musisz dodać klucz rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<dzierżawy identyfikator GUID >** .
   Z **dzierżawy identyfikator GUID** klucza, Utwórz nową wartość ciągu wielokrotnego (REG-MULTI-SZ) o nazwie **AllowedRMSServerUrls**. W przypadku własnych danych należy określić licencjonowania punktu dystrybucji, adresy URL z innych dzierżaw usługi Azure, do których uzyskuje dostęp urządzenia.
3. Adresy URL licencjonowania punktu dystrybucji można znaleźć, uruchamiając **Get-AadrmConfiguration** polecenia cmdlet w module AADRM. Jeśli wartości **LicensingIntranetDistributionPointUrl** i **LicensingExtranetDistributionPointUrl** są różne, określ obie wartości. Jeśli wartości są takie same, określ wartość tylko raz.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Jakie są opcje Ustawienia roamingu, dla istniejących aplikacji klasycznych Windows?
Roaming działa tylko dla aplikacji Universal Windows apps. Dostępne są dwie opcje umożliwiające mobilnego na istniejącą aplikację pulpitu Windows:

* [Desktop Bridge](https://aka.ms/desktopbridge) pomaga przenieść istniejące aplikacje pulpitu Windows Universal platformę Windows. W tym miejscu minimalnych zmianach w kodzie, będą musieli korzystać z zalet mobilne dane aplikacji w usłudze Azure AD. Desktop Bridge udostępnia swoje aplikacje za pomocą tożsamości aplikacji, co jest wymagane do włączenia danych aplikacji mobilnych dla istniejących aplikacji komputerowych.
* [Wirtualizacji środowiska użytkownika (wirtualizacji środowiska użytkownika)](https://technet.microsoft.com/library/dn458947.aspx) pomaga utworzyć szablon ustawienia niestandardowe dla istniejących aplikacji komputerowych Windows i włączanie roamingu dla aplikacji Win32. Ta opcja nie wymaga deweloperem aplikacji, aby zmienić kod aplikacji. Wirtualizacji środowiska użytkownika jest ograniczona do lokalnej usługi Active Directory mobilne dla klientów, którzy kupili pakietu Microsoft Desktop Optimization Pack.

Administratorzy mogą skonfigurować wirtualizacji środowiska użytkownika są przekazywane dane klasycznych aplikacji Windows, zmieniając roaming ustawień systemu operacyjnego Windows i danych aplikacji uniwersalnej za pośrednictwem [wirtualizacji środowiska użytkownika, zasady grupy](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), w tym:

* Zasady grupy systemu Windows ustawienia roamingu
* Nie Synchronizuj aplikacje Windows zasad grupy
* W sekcji aplikacji w programie Internet Explorer

W przyszłości Microsoft może zbadać sposobów na głęboko zintegrowane Windows wirtualizacji środowiska użytkownika i rozszerzyć wirtualizacji środowiska użytkownika z roamingu ustawień za pomocą chmury usługi Azure AD.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Czy mogę przechowywać zsynchronizowanych ustawień i danych w środowisku lokalnym?
Roaming stanu dla przedsiębiorstw przechowuje wszystkie zsynchronizowane dane w chmurze firmy Microsoft. Wirtualizacji środowiska użytkownika oferuje lokalnych rozwiązań mobilnych.

## <a name="who-owns-the-data-thats-being-roamed"></a>Kto jest właścicielem danych, które jest objęte roamingiem?
Przedsiębiorstwom własne dane są przekazywane za pośrednictwem Roaming stanu dla przedsiębiorstw. Dane są przechowywane w centrum danych platformy Azure. Wszystkie dane użytkownika są szyfrowane zarówno podczas przesyłania i przechowywane w chmurze przy użyciu usługi Azure Rights Management z usługi Azure Information Protection. Jest to ulepszenia w porównaniu do synchronizacji ustawieniami opartymi na konto Microsoft, który szyfruje tylko niektórych poufnych danych, takich jak poświadczenia użytkownika przed opuszczeniem urządzenia.

Firma Microsoft jest zaangażowana w ochronę danych klienta. Dane ustawienia użytkownika w przedsiębiorstwie są automatycznie szyfrowane przez usługę Azure Rights Management przed opuszczeniem urządzenia systemu Windows 10, tak aby żaden inny użytkownik może odczytywać te dane. Jeśli Twoja organizacja ma płatnej subskrypcji usługi Azure Rights Management, można używać innych funkcji ochrony, takie jak śledzenie i odwoływanie dokumentów, automatycznie chronić wiadomości e-mail zawierające poufne informacje i zarządzać własnymi kluczami ("bring Your own key"rozwiązanie także znana jako BYOK). Aby uzyskać więcej informacji o tych funkcjach i jak działa ta usługa ochrony, zobacz [co to jest Azure Rights Management](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Może zarządzać synchronizacji dla określonej aplikacji lub ustawienia?
W systemie Windows 10 nie ma zarządzania urządzeniami Przenośnymi lub zasad grupy ustawienia można wyłączyć roamingu dla poszczególnych aplikacji. Administratorzy dzierżawy mogą wyłączyć appdata synchronizacji dla wszystkich aplikacji na zarządzanym urządzeniu, ale ma nie bardziej precyzyjną kontrolę na poziomie aplikacji lub w ramach aplikacji.

## <a name="how-can-i-enable-or-disable-roaming"></a>Jak włączyć lub wyłączyć mobilnych?
W **ustawienia** aplikację, przejdź do **kont** > **synchronizować ustawienia**. Na tej stronie zostanie wyświetlony, konto, które jest używane do ustawienia roamingu, a można włączyć lub wyłączyć poszczególne grupy ustawień, które można korzystania z roamingu.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Co to jest firma Microsoft zaleca się włączania roamingu w systemie Windows 10?
Firma Microsoft ma kilka różnych ustawień roamingu rozwiązań, takich jak profile użytkowników mobilnych, wirtualizacji środowiska użytkownika i roamingu stanu przedsiębiorstwa.  Firma Microsoft jest zaangażowana w tworzenie inwestycji w przedsiębiorstwie, stan roamingu w przyszłych wersjach systemu Windows. Jeśli Twoja organizacja nie jest gotowe lub doświadczenia z przenoszeniem danych do chmury, zalecamy użycie wirtualizacji środowiska użytkownika jako podstawowej technologii mobilnych. Jeśli Twoja organizacja wymaga roaming obsługę istniejących aplikacji klasycznych Windows, ale chce przenieść do chmury, firma Microsoft zaleca korzystanie z roamingu stanu przedsiębiorstwa i wirtualizacji środowiska użytkownika. Mimo że wirtualizacji środowiska użytkownika i Roaming stanu dla przedsiębiorstw są bardzo podobne technologie, nie są one wzajemnie się wykluczają. Uzupełniają one wzajemnie, aby mieć pewność, że Twoja organizacja udostępnia usług mobilnych, które użytkownicy muszą.  

Korzystając z Roaming stanu dla przedsiębiorstw i wirtualizacji środowiska użytkownika, mają zastosowanie następujące reguły:

* Roaming stanu dla przedsiębiorstw jest podstawowym agenta roamingu na urządzeniu. Wirtualizacji środowiska użytkownika jest używany jako uzupełnienie "przerwa Win32".
* Powinno być wyłączone wirtualizacji środowiska użytkownika mobilnego dla ustawień Windows i nowoczesnych danych aplikacji platformy uniwersalnej systemu Windows, gdy zasady za pomocą grupy wirtualizacji środowiska użytkownika. Te są już objęte Roaming stanu dla przedsiębiorstw.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Jak usługa Roaming stanu dla przedsiębiorstw obsługuje infrastruktury pulpitu wirtualnego (VDI)?
Roaming stanu dla przedsiębiorstw jest obsługiwana w kliencie systemu Windows 10 jednostek SKU, ale nie na serwerze jednostki SKU. Jeśli możesz zdalnie Zaloguj się do maszyny wirtualnej maszyny Wirtualnej klienta znajduje się na komputerze funkcji hypervisor, dane są przenoszone. Jeśli wielu użytkowników współużytkować ten sam system operacyjny i zdalnego logowania do serwera w celu korzystania z pełnej pulpitu, roaming może nie działać. Ostatni scenariusz oparte na sesji nie jest oficjalnie obsługiwany.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Co się stanie, gdy Moja organizacja nabywa subskrypcję obejmuje usługę Azure Rights Management po zakończeniu korzystania z roamingu?
Jeśli Twoja organizacja używa już roamingu w systemie Windows 10 z ograniczeniami użycia usługi Azure Rights Management w bezpłatnej subskrypcji, zakup [płatną subskrypcję](https://azure.microsoft.com/pricing/details/information-protection/) zawierającej bez ochrony usługi Azure Rights Management żadnego wpływu na działanie funkcji roamingu i bez zmian konfiguracji będzie wymagane przez administratora IT.

## <a name="known-issues"></a>Znane problemy
Można znaleźć w dokumentacji w [Rozwiązywanie problemów z](enterprise-state-roaming-troubleshooting.md) sekcja zawiera listę znanych problemów. 

## <a name="next-steps"></a>Kolejne kroki 

Aby uzyskać przegląd, zobacz [Przegląd roaming stanu dla przedsiębiorstw](enterprise-state-roaming-overview.md)
