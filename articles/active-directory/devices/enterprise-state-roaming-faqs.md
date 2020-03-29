---
title: — często zadawane pytania dotyczące roamingu w stanie przedsiębiorstwa — usługa Azure Active Directory
description: Często zadawane pytania dotyczące ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad76835b0c72b691e1ef8810f2c58dedb8f597d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672389"
---
# <a name="settings-and-data-roaming-faq"></a>Roaming ustawień i danych — często zadawane pytania

Ten artykuł zawiera odpowiedzi na niektóre pytania, jakie mogą mieć administratorzy IT dotyczące ustawień i synchronizacji danych aplikacji.

## <a name="what-data-roams"></a>Jakie dane wędrują?

**Ustawienia systemu Windows**: ustawienia komputera wbudowane w system operacyjny Windows. Ogólnie rzecz biorąc, są to ustawienia, które personalizują komputer i obejmują następujące szerokie kategorie:

* *Motyw*, który zawiera funkcje, takie jak motyw pulpitu i ustawienia paska zadań.
* *Ustawienia programu Internet Explorer*, w tym ostatnio otwarte karty i ulubione.
* *Ustawienia przeglądarki Microsoft Edge*, takie jak ulubione i listy lektur.
* *Hasła*, w tym hasła internetowe, profile Wi-Fi i inne.
* *Preferencje językowe*, które obejmują ustawienia układów klawiatury, języka systemowego, daty i godziny i innych.
* *Łatwość dostępu do funkcji,* takich jak motyw o wysokim kontraście, Narrator i Lupa.
* *Inne ustawienia systemu Windows,* takie jak ustawienia myszy.

> [!NOTE]
> Ten artykuł dotyczy przeglądarki microsoft edge legacy w formacie HTML uruchomionej w systemie Windows 10 w lipcu 2015 r. Artykuł nie ma zastosowania do nowej przeglądarki opartej na chromie Microsoft Edge, wydanej 15 stycznia 2020 roku. Aby uzyskać więcej informacji na temat zachowania synchronizacji dla nowej przeglądarki Microsoft Edge, zobacz artykuł [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

**Dane aplikacji**: Uniwersalne aplikacje systemu Windows mogą zapisywać dane ustawień w folderze mobilnym, a wszelkie dane zapisane w tym folderze zostaną automatycznie zsynchronizowane. To do indywidualnego dewelopera aplikacji do projektowania aplikacji, aby skorzystać z tej możliwości. Aby uzyskać więcej informacji na temat tworzenia aplikacji uniwersalnego systemu Windows korzystającej z roamingu, zobacz [interfejs API magazynu danych aplikacji](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) i blog [dewelopera mobilnego danych aplikacji systemu Windows 8](https://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Jakie konto jest używane do synchronizacji ustawień?

W systemie Windows 8.1 synchronizacja ustawień zawsze używane konta Microsoft konsumentów. Użytkownicy korporacyjni mieli możliwość połączenia konta Microsoft z kontem domeny usługi Active Directory w celu uzyskania dostępu do synchronizacji ustawień. W systemie Windows 10 ta połączona funkcja konta Microsoft jest zastępowana strukturą konta podstawowego/pomocniczego.

Konto podstawowe jest definiowane jako konto używane do logowania się do systemu Windows. Może to być konto Microsoft, konto usługi Azure Active Directory (Azure AD), lokalne konto usługi Active Directory lub konto lokalne. Oprócz konta podstawowego użytkownicy systemu Windows 10 mogą dodawać do urządzenia co najmniej jedno dodatkowe konta w chmurze. Konto dodatkowe to zazwyczaj konto Microsoft, konto usługi Azure AD lub inne konto, takie jak Gmail lub Facebook. Te konta pomocnicze zapewniają dostęp do dodatkowych usług, takich jak logowanie jednokrotne i Sklep Windows, ale nie są w stanie włączyć synchronizacji ustawień.

W systemie Windows 10 do synchronizacji ustawień można używać tylko konta podstawowego urządzenia (zobacz [Jak uaktualnić z synchronizacji ustawień konta Microsoft w systemie Windows 8 do synchronizacji ustawień usługi Azure AD w systemie Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)

Dane nigdy nie są mieszane między różnymi kontami użytkowników na urządzeniu. Istnieją dwie reguły synchronizacji ustawień:

* Ustawienia systemu Windows zawsze będą poruszać się po koncie podstawowym.
* Dane aplikacji zostaną oznaczone kontem używanym do uzyskania aplikacji. Synchronizowane będą tylko aplikacje oznaczone kontem podstawowym. Tagowanie własności aplikacji jest określane, gdy aplikacja jest ładowana po stronie przez Sklep Windows lub zarządzanie urządzeniami przenośnymi (MDM).

Jeśli nie można zidentyfikować właściciela aplikacji, będzie on poruszać się po koncie podstawowym. Jeśli urządzenie zostanie uaktualnione z systemu Windows 8 lub Windows 8.1 do systemu Windows 10, wszystkie aplikacje zostaną oznaczone jako nabyte przez konto Microsoft. Dzieje się tak, ponieważ większość użytkowników uzyskuje aplikacje za pośrednictwem Sklepu Windows i nie było obsługi kont usługi Azure AD w Sklepie Windows przed systemem Windows 10. Jeśli aplikacja jest zainstalowana za pośrednictwem licencji w trybie offline, aplikacja zostanie oznaczona przy użyciu konta podstawowego na urządzeniu.

> [!NOTE]
> Urządzenia z systemem Windows 10, które należą do przedsiębiorstwa i są połączone z usługą Azure AD, nie mogą już łączyć swoich kont Microsoft z kontem domeny. Możliwość połączenia konta Microsoft z kontem domeny i synchronizacji wszystkich danych użytkownika z kontem Microsoft (czyli kontem Microsoft w roamingu za pośrednictwem podłączonego konta Microsoft i funkcji usługi Active Directory) jest usuwana z systemu Windows 10 urządzenia, które są przyłączone do połączonej usługi Active Directory lub środowiska usługi Azure AD.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Jak uaktualnić synchronizację ustawień konta Microsoft w systemie Windows 8 z synchronizacją ustawień usługi Azure AD w systemie Windows 10?

Jeśli użytkownik zostanie przyłączony do domeny usługi Active Directory z systemem Windows 8.1 z połączonym kontem Microsoft, zsynchronizujesz ustawienia za pośrednictwem konta Microsoft. Po uaktualnieniu do systemu Windows 10 będziesz kontynuować synchronizację ustawień użytkownika za pośrednictwem konta Microsoft, o ile użytkownik jest użytkownikiem przyłączanym do domeny, a domena usługi Active Directory nie łączy się z usługą Azure AD.

Jeśli lokalna domena usługi Active Directory łączy się z usługą Azure AD, urządzenie spróbuje zsynchronizować ustawienia przy użyciu połączonego konta usługi Azure AD. Jeśli administrator usługi Azure AD nie włączy roamingu w stanie przedsiębiorstwa, połączone konto usługi Azure AD zatrzyma ustawienia synchronizacji. Jeśli jesteś użytkownikiem systemu Windows 10 i logujesz się za pomocą tożsamości usługi Azure AD, rozpoczniesz synchronizację ustawień systemu Windows, gdy tylko administrator włączy synchronizację ustawień za pośrednictwem usługi Azure AD.

Jeśli przechowywane są dane osobowe na urządzeniu firmowym, należy pamiętać, że system operacyjny Windows i dane aplikacji rozpoczną synchronizację z usługą Azure AD. Ma to następujące implikacje:

* Ustawienia osobistego konta Microsoft będą się oddalać od ustawień na kontach usługi Azure AD w pracy lub szkole. Dzieje się tak, ponieważ synchronizacja ustawień konta Microsoft i usługi Azure AD jest teraz przy użyciu oddzielnych kont.
* Dane osobowe, takie jak hasła sieci Wi-Fi, poświadczenia sieci Web i ulubione programu Internet Explorer, które zostały wcześniej zsynchronizowane za pośrednictwem połączonego konta Microsoft, będą synchronizowane za pośrednictwem usługi Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Jak działa współdziałanie konta Microsoft i usługi Azure AD Enterprise State Roaming?

W wersjach systemu Windows 10 z listopada 2015 r. lub nowszych roamingu w stanie przedsiębiorstwa jest obsługiwany tylko dla jednego konta naraz. Jeśli zalogujesz się do systemu Windows przy użyciu służbowego konta usługi Azure AD, wszystkie dane zostaną zsynchronizowane za pośrednictwem usługi Azure AD. Jeśli zalogujesz się do systemu Windows przy użyciu osobistego konta Microsoft, wszystkie dane zostaną zsynchronizowane za pośrednictwem konta Microsoft. Uniwersalne dane aplikacji będą poruszać się tylko przy użyciu podstawowego konta logowania na urządzeniu i będą się przemieszczać tylko wtedy, gdy licencja aplikacji jest własnością konta podstawowego. Uniwersalne dane aplikacji dla aplikacji należących do kont dodatkowych nie będą synchronizowane.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Czy ustawienia są synchronizowane dla kont usługi Azure AD z wielu dzierżaw?

Gdy wiele kont usługi Azure AD z różnych dzierżaw usługi Azure AD znajduje się na tym samym urządzeniu, należy zaktualizować rejestr urządzenia, aby komunikować się z usługą Azure Rights Management dla każdej dzierżawy usługi Azure AD.  

1. Znajdź identyfikator GUID dla każdej dzierżawy usługi Azure AD. Otwórz witrynę Azure portal i wybierz dzierżawę usługi Azure AD. Identyfikator GUID dla dzierżawy znajduje się na stroniehttps://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)Właściwości dla wybranej **dzierżawy**( , oznaczona jako identyfikator katalogu . 
2. Po utworzeniu identyfikatora GUID należy dodać HKEY_LOCAL_MACHINE **identyfikatora GUI>D identyfikatora dzierżawy rejestru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<identyfikatora dzierżawy **.
   Z klucza **identyfikatora guid dzierżawy** utwórz nową wartość wielostrumieniową (REG-MULTI-SZ) o nazwie **AllowedRMSServerUrls**. Dla swoich danych określ adresy URL punktów dystrybucji licencjonowania innych dzierżaw platformy Azure, do których uzyskuje dostęp urządzenie.
3. Adresy URL punktów dystrybucji licencjonowania można znaleźć, uruchamiając polecenie cmdlet **Get-AadrmConfiguration** z modułu AADRM. Jeśli wartości **dla LicensingIntranetDistributionPointUrl** i **LicensingExtranetDistributionPointUrl** są różne, określ obie wartości. Jeśli wartości są takie same, należy określić wartość tylko raz.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Jakie są opcje ustawień roamingu dla istniejących aplikacji klasycznych systemu Windows?

Roaming działa tylko w przypadku aplikacji uniwersalnych systemu Windows. Dostępne są dwie opcje włączania roamingu w istniejącej aplikacji klasycznej systemu Windows:

* Mostek [pulpitu](https://aka.ms/desktopbridge) ułatwia przeprowadzenie istniejących aplikacji klasycznych systemu Windows na platformę uniwersalną systemu Windows. W tym miejscu minimalne zmiany kodu będą wymagane, aby korzystać z roamingu danych aplikacji usługi Azure AD. Mostek pulpitu udostępnia aplikacjom tożsamość aplikacji, która jest potrzebna do włączenia roamingu danych aplikacji dla istniejących aplikacji klasycznych.
* [Wirtualizacja środowiska użytkownika (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) ułatwia tworzenie niestandardowego szablonu ustawień dla istniejących aplikacji klasycznych systemu Windows i włącza roaming aplikacji Win32. Ta opcja nie wymaga od dewelopera aplikacji zmiany kodu aplikacji. Ue-V jest ograniczona do lokalnego roamingu usługi Active Directory dla klientów, którzy zakupili pakiet Microsoft Desktop Optimization Pack.

Administratorzy mogą skonfigurować ue-V do poruszania się danymi aplikacji klasycznych systemu Windows, zmieniając roaming ustawień systemu operacyjnego Windows i danych aplikacji uniwersalnych za pośrednictwem [zasad grupy UE-V,](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2)w tym:

* Zasady grupy ustawień systemu Windows
* Nie synchronizuj zasad grupy aplikacji systemu Windows Apps
* Roaming programu Internet Explorer w sekcji aplikacje

W przyszłości firma Microsoft może zbadać sposoby, aby UE-V głęboko zintegrowane z systemem Windows i rozszerzyć UE-V do poruszania się ustawień za pośrednictwem chmury usługi Azure AD.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Czy mogę przechowywać zsynchronizowane ustawienia i dane lokalnie?

Roaming stanu przedsiębiorstwa przechowuje wszystkie zsynchronizowane dane w chmurze firmy Microsoft. UE-V oferuje lokalne rozwiązanie mobilne.

## <a name="who-owns-the-data-thats-being-roamed"></a>Kto jest właścicielem danych, które są przemierzane?

Przedsiębiorstwa są właścicielami danych, które są przemierzane za pośrednictwem roamingu stanu przedsiębiorstwa. Dane są przechowywane w centrum danych platformy Azure. Wszystkie dane użytkownika są szyfrowane zarówno podczas przesyłania, jak i w spoczynku w chmurze przy użyciu usługi Azure Rights Management z usługi Azure Information Protection. Jest to ulepszenie w porównaniu do synchronizacji ustawień opartych na koncie Microsoft, która szyfruje tylko niektóre poufne dane, takie jak poświadczenia użytkownika przed opuszczeniem urządzenia.

Firma Microsoft dokłada wszelkich starań, aby chronić dane klientów. Dane ustawień użytkownika przedsiębiorstwa są automatycznie szyfrowane przez usługę Azure Rights Management przed opuszczeniem urządzenia z systemem Windows 10, więc żaden inny użytkownik nie może odczytać tych danych. Jeśli twoja organizacja ma płatną subskrypcję usługi Azure Rights Management, możesz użyć innych funkcji ochrony, takich jak śledzenie i odwoływanie dokumentów, automatyczne chronienie wiadomości e-mail zawierających poufne informacje i zarządzanie własnymi kluczami ( własnego klucza", znanego również jako BYOK). Aby uzyskać więcej informacji na temat tych funkcji i sposobu działania tej usługi ochrony, zobacz [Co to jest usługa Azure Rights Management](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Czy mogę zarządzać synchronizacją dla określonej aplikacji lub ustawienia?

W systemie Windows 10 nie ma ustawienia mdm ani zasad grupy, aby wyłączyć roaming dla pojedynczej aplikacji. Administratorzy dzierżawy mogą wyłączyć synchronizację danych aplikacji dla wszystkich aplikacji na zarządzanym urządzeniu, ale nie ma lepszą kontrolę na poziomie aplikacji lub w obrębie aplikacji.

## <a name="how-can-i-enable-or-disable-roaming"></a>Jak włączyć lub wyłączyć roaming?

W aplikacji **Ustawienia** przejdź do **sekcji Synchronizowanie** > **ustawień**kont . Na tej stronie możesz zobaczyć, które konto jest używane do poruszania się po ustawieniach, i możesz włączyć lub wyłączyć poszczególne grupy ustawień, które mają być przemierzane.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Jakie jest zalecenie firmy Microsoft dotyczące włączania roamingu w systemie Windows 10?

Firma Microsoft ma kilka różnych ustawień dostępnych rozwiązań mobilnych, w tym mobilne profile użytkowników, UE-V i roaming stanu przedsiębiorstwa.  Firma Microsoft zobowiązuje się do inwestowania w roaming w stanie przedsiębiorstwa w przyszłych wersjach systemu Windows. Jeśli twoja organizacja nie jest gotowa lub wygodna w przenoszeniu danych do chmury, zaleca się używanie ue-V jako podstawowej technologii roamingu. Jeśli twoja organizacja wymaga obsługi roamingu dla istniejących aplikacji klasycznych systemu Windows, ale chętnie przenosi się do chmury, zaleca się korzystanie z roamingu w stanie przedsiębiorstwa i interfejsu UE-V. Chociaż UE-V i Enterprise State Roaming są bardzo podobne technologie, nie wykluczają się wzajemnie. Uzupełniają się wzajemnie, aby zapewnić, że twoja organizacja zapewnia usługi mobilne, których potrzebują użytkownicy.  

W przypadku korzystania z roamingu w stanie przedsiębiorstwa i UE-V obowiązują następujące reguły:

* Roaming w stanie przedsiębiorstwa jest podstawowym agentem mobilnym na urządzeniu. UE-V jest używany do uzupełnienia "Luki Win32."
* Roaming UE-V dla ustawień systemu Windows i nowoczesnych danych aplikacji platformy uniwersalnej systemu Windows powinien być wyłączony podczas korzystania z zasad grupy UE-V. Są one już objęte roamingiem stanu przedsiębiorstwa.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>W jaki sposób roaming stanu przedsiębiorstwa obsługuje infrastrukturę pulpitu wirtualnego (VDI)?

Roaming stanu przedsiębiorstwa jest obsługiwany w jednostkach SKU klienta systemu Windows 10, ale nie na jednostkach SKU serwera. Jeśli maszyna wirtualna klienta jest hostowana na komputerze hipernadzorcy i zdalnie logujesz się na maszynie wirtualnej, dane będą się przemieszczać. Jeśli wielu użytkowników współużytkuje ten sam system operacyjny, a użytkownicy zdalnie logują się do serwera w celu uzyskania pełnego środowiska pulpitu, roaming może nie działać. Ten ostatni scenariusz oparty na sesji nie jest oficjalnie obsługiwany.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Co się stanie, gdy moja organizacja kupi subskrypcję obejmującą usługę Azure Rights Management po użyciu roamingu?

Jeśli Twoja organizacja korzysta już z roamingu w systemie Windows 10 z bezpłatną subskrypcją usługi Azure Rights Management o ograniczonym użyciu, zakup [płatnej subskrypcji](https://azure.microsoft.com/pricing/details/information-protection/) zawierającej usługę ochrony usługi Azure Rights Management nie będzie miał żadnego wpływu na funkcjonalność funkcji mobilnej i administrator IT nie będzie wymagał żadnych zmian konfiguracji.

## <a name="known-issues"></a>Znane problemy

Zobacz dokumentację w sekcji [rozwiązywania problemów,](enterprise-state-roaming-troubleshooting.md) aby uzyskać listę znanych problemów. 

## <a name="next-steps"></a>Następne kroki 

Aby uzyskać omówienie, zobacz [omówienie roamingu w stanie przedsiębiorstwa](enterprise-state-roaming-overview.md)
