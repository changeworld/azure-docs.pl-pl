---
title: Często zadawane pytania dotyczące połączenia usługi Azure Active Directory - | Dokumenty firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5aa30bc819531ee8cc9cd337648a6cbc661bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149817"
---
# <a name="azure-active-directory-connect-faq"></a>Często zadawane pytania dotyczące połączenia usługi Azure Active Directory

## <a name="general-installation"></a>Instalacja ogólna

**Pyt.: Jak mogę wzmocnić mój serwer usługi Azure AD Connect, aby zmniejszyć powierzchnię ataku zabezpieczeń?**

Firma Microsoft zaleca wzmocnienie serwera usługi Azure AD Connect w celu zmniejszenia powierzchni ataku zabezpieczeń dla tego krytycznego składnika środowiska IT.  Przestrzeganie poniższych zaleceń zmniejszy zagrożenia bezpieczeństwa dla twojej organizacji.

* Wdrażanie usługi Azure AD Connect na serwerze przyłączanym do domeny i ograniczanie dostępu administracyjnego do administratorów domeny lub innych ściśle kontrolowanych grup zabezpieczeń

Aby dowiedzieć się więcej, zobacz: 

* [Zabezpieczanie grup administratorów](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Zabezpieczanie wbudowanych kont administratorów](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Poprawa bezpieczeństwa i utrzymanie poprzez zmniejszenie powierzchni ataku](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Zmniejszanie powierzchni ataku usługi Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**Pyt.: Czy instalacja będzie działać, jeśli administrator globalny usługi Azure Active Directory (Azure AD) ma włączone uwierzytelnianie dwuskładnikowe (2FA)?**  
Od kompilacji z lutego 2016 r. ten scenariusz jest obsługiwany.

**Pyt.: Czy istnieje sposób instalowania usługi Azure AD Connect bez nadzoru?**  
Instalacja usługi Azure AD Connect jest obsługiwana tylko podczas korzystania z kreatora instalacji. Instalacja nienadzorowana, cicha nie jest obsługiwana.

**P: Mam las, z którym nie można skontaktować się z jedną domeną. Jak zainstalować usługę Azure AD Connect?**  
Od kompilacji z lutego 2016 r. ten scenariusz jest obsługiwany.

**Pyt.: Czy agent kondycji Usług domenowych Active Directory platformy Azure (Azure AD DS) działa na rdzeniu serwera?**  
Tak. Po zainstalowaniu agenta można ukończyć proces rejestracji przy użyciu następującego polecenia cmdlet programu PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Pyt.: Czy usługa Azure AD Connect obsługuje synchronizowanie z dwóch domen do usługi Azure AD?**  
Tak, ten scenariusz jest obsługiwany. Zapoznaj się [z wieloma domenami](how-to-connect-install-multiple-domains.md).
 
**Pyt.: Czy możesz mieć wiele łączników dla tej samej domeny usługi Active Directory w usłudze Azure AD Connect?**  
Nie, wiele łączników dla tej samej domeny usługi AD nie jest obsługiwanych. 

**Pyt.: Czy można przenieść bazę danych usługi Azure AD Connect z lokalnej bazy danych do zdalnego wystąpienia programu SQL Server?**   
Tak, poniższe kroki zawierają ogólne wskazówki, jak to zrobić. Obecnie pracujemy nad bardziej szczegółowym dokumentem.
1. Tworzenie kopii zapasowej bazy danych LocalDB ADSync.
Najprostszym sposobem, aby to zrobić, jest użycie programu SQL Server Management Studio zainstalowanego na tym samym komputerze co usługa Azure AD Connect. Połącz się *z (LocalDb).\ADSync*, a następnie połącz ze strony adsync bazy danych.

2. Przywracanie bazy danych ADSync do zdalnego wystąpienia programu SQL Server.

3. Zainstaluj usługę Azure AD Connect względem istniejącej [zdalnej bazy danych SQL](how-to-connect-install-existing-database.md).
   W tym artykule pokazano, jak przeprowadzić migrację do przy użyciu lokalnej bazy danych SQL. W przypadku migracji do zdalnej bazy danych SQL, w kroku 5 procesu należy również wprowadzić istniejące konto usługi, które będzie działać jako usługa Synchronizacja systemu Windows. To konto usługi aparatu synchronizacji jest opisane w tym miejscu:
   
      **Użyj istniejącego konta usługi:** Domyślnie usługa Azure AD Connect używa konta usługi wirtualnej dla usług synchronizacji do użycia. Jeśli używasz zdalnego wystąpienia programu SQL Server lub używasz serwera proxy, który wymaga uwierzytelniania, użyj konta usługi zarządzanej lub konta usługi w domenie i zna hasło. W takich przypadkach wprowadź konto do użycia. Upewnij się, że użytkownicy, którzy są uruchomione instalacji są administratorami systemu w języku SQL, dzięki czemu można utworzyć poświadczenia logowania dla konta usługi. Aby uzyskać więcej informacji, zobacz [Konta i uprawnienia usługi Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      Od ostatniej kompilacji aprowizowanie bazy danych może wykonać poza pasmem administrator usługi SQL, a jej instalację może następnie przeprowadzić administrator programu Azure AD Connect z uprawnieniami właściciela bazy danych. Aby uzyskać więcej informacji, zobacz [Instalowanie usługi Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL](how-to-connect-install-sql-delegation.md).

Aby zachować prostotę, zaleca się, aby użytkownicy, którzy instalują usługę Azure AD Connect, byliby administratorami systemu w języku SQL. Jednak w przypadku najnowszych kompilacji można teraz używać delegowanych administratorów SQL, zgodnie z opisem w [programie Zainstaluj usługę Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL](how-to-connect-install-sql-delegation.md).

**Pyt.: Jakie są najlepsze rozwiązania z pola?**  

Poniżej znajduje się dokument informacyjny, który przedstawia niektóre z najlepszych praktyk, które inżynieria, wsparcie i nasi konsultanci opracowali na przestrzeni lat.  Jest to przedstawione na liście punktorów, do których można szybko się odwoływać.  Mimo że ta lista próbuje być wyczerpująca, mogą istnieć dodatkowe najlepsze rozwiązania, które mogą nie znaleźć się jeszcze na liście.

- Jeśli używasz pełnego SQL, to powinien pozostać lokalny vs zdalny
    - Mniej przeskoków
    - Łatwiejsze rozwiązywanie problemów
    - Mniejsza złożoność
    - Trzeba wyznaczyć zasoby do sql i zezwolić na obciążenie dla usługi Azure AD Connect i systemu operacyjnego
- Omiń serwer proxy, jeśli w ogóle możliwe, jeśli nie można pominąć serwera proxy, należy upewnić się, że wartość limitu czasu jest większa niż 5 minut.
- Jeśli wymagany jest serwer proxy, należy dodać serwer proxy do pliku machine.config
- Należy pamiętać o lokalnych zadaniach SQL i konserwacji i ich wpływ na usługę Azure AD Connect — w szczególności ponowne indeksowanie
- Upewnij się, że system DNS może rozwiązać problem zewnętrznie
- Upewnij się, że [specyfikacje serwera](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) są na zalecenie, niezależnie od tego, czy używasz serwerów fizycznych, czy wirtualnych
- Upewnij się, że w przypadku korzystania z serwera wirtualnego wymagane zasoby są
- Upewnij się, że konfiguracja dysku i dysku spełnia najważniejsze wskazówki dotyczące programu SQL Server
- Instalowanie i konfigurowanie usługi Azure AD Connect Health do monitorowania
- Użyj progu usuwania wbudowanego w usługę Azure AD Connect.
- Starannie przejrzyj aktualizacje wersji, które mają być przygotowane na wszystkie zmiany i nowe atrybuty, które mogą zostać dodane
- Tworzenie kopii zapasowych wszystkiego
    - Klucze kopii zapasowej
    - Reguły synchronizacji kopii zapasowych
    - Konfiguracja serwera kopii zapasowych
    - Kopia zapasowa bazy danych SQL
- Upewnij się, że nie ma agentów kopii zapasowych innych firm, które tworzą kopię zapasową SQL bez modułu SQL VSS Writer (często na serwerach wirtualnych z migawkami innych firm)
- Ogranicz liczbę niestandardowych reguł synchronizacji, które są używane w miarę zwiększania złożoności
- Traktuj serwery usługi Azure AD Connect jako serwery warstwy 0
- Bądź skąpy w modyfikowaniu reguł synchronizacji chmury bez doskonałego zrozumienia wpływu i odpowiednich sterowników biznesowych
- Upewnij się, że prawidłowe porty adresów URL i zapory są otwarte dla obsługi usługi Azure AD Connect i usługi Azure AD Connect Health
- Wykorzystaj atrybut filtrowany w chmurze, aby rozwiązywać problemy z obiektami fantomowymi i zapobiegać im
- Z serwerem przemieszczania upewnij się, że używasz dokumentu konfiguracji usługi Azure AD Connect w celu zapewnienia spójności między serwerami
- Serwery przemieszczania powinny znajdować się w oddzielnych centrach danych (lokalizacje fizyczne
- Serwery przejściowe nie mają być rozwiązaniem o wysokiej dostępności, ale mogą mieć wiele serwerów przejściowych
- Wprowadzenie serwerów przejściowych "Lag" może zmniejszyć niektóre potencjalne przestoje w przypadku błędu
- Najpierw przetestuj i sprawdź poprawność wszystkich uaktualnień na serwerze przemieszczania
- Zawsze sprawdzaj poprawność eksportu przed przełączeniem na serwer przejściowy.  Wykorzystaj serwer przejściowy do pełnego importu i pełnej synchronizacji, aby zmniejszyć wpływ na działalność
- Zachowaj spójność wersji między serwerami Usługi Azure AD Connect w miarę możliwości 

**Pyt.: Czy mogę zezwolić usłudze Azure AD Connect na utworzenie konta usługi Azure AD Connector na komputerze grupy roboczej?**
Nie.  Aby umożliwić usłudze Azure AD Connect automatyczne tworzenie konta usługi Azure AD Connector, komputer musi być przyłączony do domeny.  

## <a name="network"></a>Network (Sieć)
**P: Mam zaporę, urządzenie sieciowe lub coś innego, co ogranicza czas, w jaki połączenia mogą pozostać otwarte w mojej sieci. Jaki powinien być próg limitu czasu po stronie klienta podczas korzystania z usługi Azure AD Connect?**  
Wszystkie oprogramowanie sieciowe, urządzenia fizyczne lub cokolwiek innego, które ogranicza maksymalny czas, w którym połączenia mogą pozostać otwarte, powinny używać progu co najmniej pięciu minut (300 sekund) dla łączności między serwerem, na którym jest zainstalowany klient usługi Azure AD Connect i usługi Azure Active Directory. To zalecenie dotyczy również wszystkich wcześniej wydanych narzędzi synchronizacji tożsamości firmy Microsoft.

**Pyt.: Czy domeny pojedynczej etykiety (SLD) są obsługiwane?**  
Chociaż zdecydowanie zaleca się przeciwko tej konfiguracji sieci[(zobacz artykuł](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), przy użyciu synchronizacji usługi Azure AD Connect z domeny pojedynczej etykiety jest obsługiwany, tak długo, jak konfiguracja sieci dla domeny jednego poziomu działa poprawnie.

**Pyt.: Czy lasy z rozłączne domeny usługi AD obsługiwane?**  
Nie, usługa Azure AD Connect nie obsługuje lokalnych lasów zawierających rozłączne przestrzenie nazw.

**P: Czy obsługiwane są nazwy NetBIOS "kropkowane"?**  
Nie, usługa Azure AD Connect nie obsługuje lokalnych lasów ani domen, w których nazwa NetBIOS zawiera kropkę (.).

**Pyt.: Czy czyste środowisko IPv6 jest obsługiwane?**  
Nie, usługa Azure AD Connect nie obsługuje czystego środowiska IPv6.

**P:I mają środowisko wielolesowe, a sieć między dwoma lasami korzysta z translatora adresów sieciowych (Translacja adresów sieciowych). Czy usługa Azure AD Connect jest obsługiwana między tymi dwoma lasami?**</br>
Nie, przy użyciu usługi Azure AD Connect przez NAT nie jest obsługiwany. 

## <a name="federation"></a>Federacja
**Pyt.: Co zrobić, jeśli otrzymuję wiadomość e-mail z prośbą o odnowienie certyfikatu usługi Office 365?**  
Aby uzyskać wskazówki dotyczące odnawiania certyfikatu, zobacz [odnawianie certyfikatów](how-to-connect-fed-o365-certs.md).

**P: Dla jednostki uzależniającej usługi Office 365 ustawiono opcję "Automatycznie aktualizuj jednostkę uzależniającą". Czy muszę podjąć jakieś działania, gdy mój certyfikat podpisywania tokenu zostanie automatycznie przewróty?**  
Skorzystaj ze wskazówek opisanych w artykule [odnawiaj certyfikaty](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Środowisko
**Pyt.: Czy po zainstalowaniu usługi Azure AD Connect jest obsługiwana zmiana nazwy serwera?**  
Nie. Zmiana nazwy serwera powoduje, że aparat synchronizacji nie może połączyć się z wystąpieniem bazy danych SQL, a usługa nie może się uruchomić.

**Pyt.: Czy reguły synchronizacji kryptograficznej nowej generacji (NGC) są obsługiwane na komputerze z obsługą FIPS?**  
Nie.  Nie są one obsługiwane.

**P. Jeśli wyłączono zsynchronizowane urządzenie (na przykład: HAADJ) w witrynie Azure portal, dlaczego jest ponownie włączone?**<br>
Zsynchronizowane urządzenia mogą być tworzyć lub opanować w środowisku lokalnym. Jeśli zsynchronizowane urządzenie jest włączone lokalnie, może być ponownie włączone w witrynie Azure portal, nawet jeśli został wcześniej wyłączony przez administratora. Aby wyłączyć zsynchronizowane urządzenie, użyj lokalnej usługi Active Directory, aby wyłączyć konto komputera.

**P. Jeśli zablokuję logowanie użytkownika w portalu usługi Office 365 lub Azure AD dla zsynchronizowanych użytkowników, dlaczego jest ono odblokowane po ponownym zalogowaniu?**<br>
Zsynchronizowani użytkownicy mogą być tworzenia lub opanowania w środowisku lokalnym. Jeśli konto jest włączone lokalnie, może odblokować blok logowania umieszczony przez administratora.

## <a name="identity-data"></a>Dane identyfikacyjne
**Pyt.: Dlaczego atrybut userPrincipalName (UPN) w usłudze Azure AD nie jest zgodny z lokalną siecią UPN?**  
Aby uzyskać więcej informacji, zobacz następujące artykuły:

* [Nazwy użytkowników w usłudze Office 365, platformie Azure lub usłudze Intune nie są zgodne z lokalną siecią UPN lub alternatywnym identyfikatorem logowania](https://support.microsoft.com/kb/2523192)
* [Zmiany nie są synchronizowane przez narzędzie do synchronizacji usługi Azure Active Directory po zmianie nazwy UPN konta użytkownika w celu użycia innej domeny federacyjnej](https://support.microsoft.com/kb/2669550)

Można również skonfigurować usługę Azure AD, aby umożliwić aparatowi synchronizacji aktualizowanie upn, zgodnie z opisem w [usłudze synchronizacji usługi Azure AD Connect.](how-to-connect-syncservice-features.md)

**Pyt.: Czy jest obsługiwana w celu dopasowania do lokalnej grupy usługi Azure AD lub obiektu kontaktu z istniejącą grupą usługi Azure AD lub obiektem kontaktu?**  
Tak, to miękkie dopasowanie jest oparte na proxyAddress. Dopasowanie miękkie nie jest obsługiwane w przypadku grup, które nie obsługują poczty.

**Pyt.: Czy jest obsługiwane ręcznie ustawić atrybut ImmutableId w istniejącej grupie usługi Azure AD lub obiektu kontaktu, aby trudno dopasować go do lokalnej grupy usługi Azure AD lub obiektu kontaktowego?**  
Nie, ręcznie ustawienie atrybutu ImmutableId w istniejącej grupie usługi Azure AD lub obiektu kontaktu na zgodne z nim jest obecnie nie obsługiwane.

## <a name="custom-configuration"></a>Konfiguracja niestandardowa
**Pyt.: Gdzie są udokumentowane polecenia cmdlet programu PowerShell dla usługi Azure AD Connect?**  
Z wyjątkiem poleceń cmdlet, które są udokumentowane w tej witrynie, inne polecenia cmdlet programu PowerShell znalezione w usłudze Azure AD Connect nie są obsługiwane do użytku klienta.

**Pyt.: Czy można użyć opcji "Eksport serwera/import serwera", która znajduje się w Menedżerze usług synchronizacji, aby przenieść konfigurację między serwerami?**  
Nie. Ta opcja nie pobiera wszystkich ustawień konfiguracji i nie powinna być używana. Zamiast tego użyj kreatora, aby utworzyć konfigurację podstawową na drugim serwerze, a edytor reguł synchronizacji służy do generowania skryptów programu PowerShell w celu przenoszenia dowolnej reguły niestandardowej między serwerami. Aby uzyskać więcej informacji, zobacz [Migracja huśtawka](how-to-upgrade-previous-version.md#swing-migration).

**Pyt.: Czy hasła mogą być buforowane dla strony logowania platformy Azure i można zapobiec temu buforowaniu, ponieważ zawiera element wprowadzania hasła z atrybutem *autouzupełniania = "false"?***  
Obecnie modyfikowanie atrybutów HTML pola **Hasło,** w tym tagu autouzupełniania, nie jest obsługiwane. Obecnie pracujemy nad funkcją, która pozwala na niestandardowy javascript, który pozwala dodać dowolny atrybut do pola **Hasło.**

**Pyt.: Strona logowania platformy Azure wyświetla nazwy użytkowników, którzy wcześniej zalogowali się pomyślnie. Czy to zachowanie można wyłączyć?**  
Obecnie modyfikowanie atrybutów HTML pola **wprowadzania hasła,** w tym tagu autouzupełniania, nie jest obsługiwane. Obecnie pracujemy nad funkcją, która pozwala na niestandardowy javascript, który pozwala dodać dowolny atrybut do pola **Hasło.**

**Pyt.: Czy istnieje sposób, aby zapobiec równoczesnych sesji?**  
Nie.

## <a name="auto-upgrade"></a>Automatyczne uaktualnianie

**P: Jakie są zalety i konsekwencje korzystania z automatycznego uaktualniania?**  
Doradzamy wszystkim klientom, aby włączyli automatyczne uaktualnianie dla ich instalacji usługi Azure AD Connect. Zaletą jest to, że zawsze otrzymujesz najnowsze poprawki, w tym aktualizacje zabezpieczeń dotyczące luk w zabezpieczeniach znalezionych w usłudze Azure AD Connect. Proces uaktualniania jest bezbolesny i odbywa się automatycznie, gdy tylko dostępna jest nowa wersja. Wiele tysięcy klientów usługi Azure AD Connect korzysta z automatycznego uaktualniania w każdej nowej wersji.

Proces automatycznego uaktualniania zawsze najpierw określa, czy instalacja kwalifikuje się do automatycznego uaktualnienia. Jeśli kwalifikuje się, uaktualnienie jest wykonywane i testowane. Proces ten obejmuje również poszukiwanie niestandardowych zmian w przepisach i konkretnych czynnikach środowiskowych. Jeśli testy wykaże, że uaktualnienie nie powiedzie się, poprzednia wersja zostanie automatycznie przywrócona.

W zależności od wielkości środowiska proces może potrwać kilka godzin. Podczas uaktualniania jest w toku, nie ma synchronizacji między usługą Active Directory systemu Windows Server i usługi Azure AD.

**P: Otrzymałem wiadomość e-mail z informacją, że moje automatyczne uaktualnienie już nie działa i muszę zainstalować nową wersję. Dlaczego muszę to zrobić?**  
W zeszłym roku firma Microsoft wydała wersję usługi Azure AD Connect, która w pewnych okolicznościach mogła wyłączyć funkcję automatycznego uaktualniania na serwerze. Rozwiązaliśmy ten problem w usłudze Azure AD Connect w wersji 1.1.750.0. Jeśli problem został naruszony, można go złagodzić, uruchamiając skrypt programu PowerShell w celu jego naprawy lub ręcznie uaktualniając go do najnowszej wersji usługi Azure AD Connect. 

Aby uruchomić skrypt programu PowerShell, [pobierz skrypt](https://aka.ms/repairaadconnect) i uruchom go na serwerze usługi Azure AD Connect w administracyjnym oknie programu PowerShell. Aby dowiedzieć się, jak uruchomić skrypt, [zobacz ten krótki film](https://aka.ms/repairaadcau).

Aby ręcznie uaktualnić uaktualnienie, należy pobrać i uruchomić najnowszą wersję pliku AADConnect.msi.
 
-  Jeśli bieżąca wersja jest starsza niż 1.1.750.0, [pobierz i uaktualnij do najnowszej wersji](https://www.microsoft.com/download/details.aspx?id=47594).
- Jeśli twoja wersja usługi Azure AD Connect jest 1.1.750.0 lub nowsza, nie jest wymagane żadne dalsze działania. Używasz już wersji zawierającej poprawkę automatycznego uaktualniania. 

**P: Otrzymałem wiadomość e-mail z informacją o uaktualnieniu do najnowszej wersji w celu ponownego włączenia automatycznego uaktualnienia. Używam wersji 1.1.654.0. Czy muszę uaktualnić?**  
Tak, aby ponownie włączyć automatyczne uaktualnianie do wersji 1.1.750.0 lub nowszej, musisz uaktualnić do wersji 1.1.750.0 lub nowszej. [Pobierz i uaktualnij do najnowszej wersji](https://www.microsoft.com/download/details.aspx?id=47594).

**P: Otrzymałem wiadomość e-mail z informacją o uaktualnieniu do najnowszej wersji w celu ponownego włączenia automatycznego uaktualnienia. Jeśli używam programu PowerShell do automatycznego uaktualniania, czy nadal muszę zainstalować najnowszą wersję?**  
Tak, nadal musisz uaktualnić do wersji 1.1.750.0 lub nowszej. Włączenie usługi automatycznego uaktualniania za pomocą programu PowerShell nie ogranicza problemu z automatycznym uaktualnieniem występującym w wersjach przed wersją 1.1.750.0.

**Pyt.: Chcę uaktualnić do nowszej wersji, ale nie jestem pewien, kto zainstalował usługę Azure AD Connect, a my nie mamy nazwy użytkownika i hasła. Czy tego potrzebujemy?**
Nie musisz znać nazwy użytkownika i hasła, które początkowo były używane do uaktualniania usługi Azure AD Connect. Użyj dowolnego konta usługi Azure AD, które ma rolę administratora globalnego.

**Pyt.: Jak mogę znaleźć wersję usługi Azure AD Connect używam?**  
Aby sprawdzić, która wersja usługi Azure AD Connect jest zainstalowana na serwerze, przejdź do Panelu sterowania i wyszukuj zainstalowaną wersję usługi Microsoft Azure AD Connect, wybierając **programy** > **i funkcje**, jak pokazano poniżej:

![Wersja usługi Azure AD Connect w Panelu sterowania](./media/reference-connect-faq/faq1.png)

**Pyt.: Jak uaktualnić do najnowszej wersji usługi Azure AD Connect?**  
Aby dowiedzieć się, jak uaktualnić do najnowszej wersji, zobacz [Azure AD Connect: Uaktualnienie z poprzedniej wersji do najnowszej](how-to-upgrade-previous-version.md). 

**P: W zeszłym roku uaktualniliśmy już do najnowszej wersji usługi Azure AD Connect. Czy musimy ponownie uaktualnić?**  
Zespół usługi Azure AD Connect wprowadza częste aktualizacje usługi. Aby korzystać z poprawek błędów i aktualizacji zabezpieczeń, a także nowych funkcji, ważne jest, aby serwer był na bieżąco z najnowszą wersją. Jeśli włączysz automatyczne uaktualnianie, wersja oprogramowania zostanie zaktualizowana automatycznie. Aby znaleźć historię wersji usługi Azure AD Connect, zobacz [Historia wydania usługi Azure AD Connect: Historia wersji](reference-connect-version-history.md).

**Pyt.: Jak długo trwa uaktualnienie i jaki jest wpływ na moich użytkowników?**  
Czas potrzebny do uaktualnienia zależy od rozmiaru dzierżawy. W przypadku większych organizacji najlepiej jest przeprowadzić uaktualnienie wieczorem lub w weekend. Podczas uaktualniania nie ma żadnych działań synchronizacji.

**P: Wierzę, że uaktualniono do usługi Azure AD Connect, ale portal pakietu Office nadal wspomina o DirSync. Dlaczego tak się dzieje?**  
Zespół pakietu Office pracuje nad uzyskaniem aktualizacji portalu pakietu Office w celu odzwierciedlenia bieżącej nazwy produktu. Nie odzwierciedla używanego narzędzia do synchronizacji.

**P: Mój stan automatycznego uaktualniania mówi "Zawieszone". Dlaczego jest zawieszony? Czy powinienem go włączyć?**  
Błąd został wprowadzony w poprzedniej wersji, który w pewnych okolicznościach pozostawia stan automatycznego uaktualniania ustawiony na "Zawieszone". Ręczne włączanie jest technicznie możliwe, ale wymagałoby kilku złożonych kroków. Najlepszą rzeczą, jaką możesz zrobić, to zainstalować najnowszą wersję usługi Azure AD Connect.

**P: Moja firma ma surowe wymagania dotyczące zarządzania zmianami i chcę kontrolować, kiedy jest wypchnięta. Czy mogę kontrolować, kiedy zostanie uruchomiona automatyczna aktualizacja?**  
Nie, nie ma dziś takiej funkcji. Funkcja jest oceniana pod kątem przyszłej wersji.

**Pyt.: Czy otrzymam wiadomość e-mail, jeśli automatyczne uaktualnienie nie powiodło się? Skąd będę wiedzieć, że to się udało?**  
Użytkownik nie zostanie powiadomiony o wyniku uaktualnienia. Funkcja jest oceniana pod kątem przyszłej wersji.

**Pyt.: Czy publikujesz oś czasu, kiedy planujesz wypchnąć automatyczne uaktualnienia?**  
Automatyczne uaktualnianie jest pierwszym krokiem w procesie wydawania nowszej wersji. Za każdym razem, gdy pojawia się nowa wersja, uaktualnienia są wypychane automatycznie. Nowsze wersje usługi Azure AD Connect są wstępnie ogłaszane w [planie działania usługi Azure AD](../fundamentals/whats-new.md).

**Pyt.: Czy automatyczne uaktualnianie również uaktualnić usługi Azure AD Connect Health?**  
Tak, automatyczne uaktualnianie uaktualnia również usługę Azure AD Connect Health.

**Pyt.: Czy można również automatycznie uaktualnić serwery usługi Azure AD Connect w trybie przejściowym?**  
Tak, można automatycznie uaktualnić serwer usługi Azure AD Connect, który jest w trybie przejściowym.

**Pyt.: Jeśli automatyczne uaktualnianie nie powiedzie się, a mój serwer Usługi Azure AD Connect nie zostanie uruchomiony, co należy zrobić?**  
W rzadkich przypadkach usługa Azure AD Connect nie uruchamia się po wykonaniu uaktualnienia. W takich przypadkach ponowne uruchomienie serwera zwykle rozwiązuje problem. Jeśli usługa Azure AD Connect nadal nie jest uruchamiana, otwórz bilet pomocy technicznej. Aby uzyskać więcej informacji, zobacz [Tworzenie żądania usługi w celu skontaktowania się z pomocą techniczną usługi Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**Pyt.: Nie jestem pewien, jakie są zagrożenia podczas uaktualniania do nowszej wersji usługi Azure AD Connect. Czy możesz zadzwonić do mnie, aby mi pomóc z uaktualnieniem?**  
Jeśli potrzebujesz pomocy w uaktualnianiu do nowszej wersji usługi Azure AD Connect, otwórz bilet pomocy technicznej w temacie Tworzenie żądania usługi w [celu skontaktowania się z pomocą techniczną usługi Office 365.](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)

## <a name="troubleshooting"></a>Rozwiązywanie problemów
**Pyt.: Jak mogę uzyskać pomoc dotyczącą usługi Azure AD Connect?**

[Wyszukiwanie w bazie wiedzy Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Wyszukaj w bazy wiedzy rozwiązania techniczne dotyczące typowych problemów z rozwiązywaniem problemów z przerwami w obsłudze usługi Azure AD Connect.

[Fora usługi Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Wyszukaj pytania techniczne i odpowiedzi lub zadaj własne pytania, przechodząc do [społeczności usługi Azure AD.](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)

[Uzyskiwanie pomocy technicznej dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

**Pyt.: Dlaczego widzę zdarzenia 6311 i 6401 występują po błędy kroku synchronizacji?**

Zdarzenia 6311 — **serwer napotkał nieoczekiwany błąd podczas wykonywania wywołania zwrotnego** i 6401 — **kontroler agenta zarządzania napotkał nieoczekiwany błąd** - są zawsze rejestrowane po błędzie kroku synchronizacji. Aby rozwiązać te błędy, należy wyczyścić błędy kroku synchronizacji.  Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z błędami podczas synchronizacji](tshoot-connect-sync-errors.md) i [rozwiązywania problemów z synchronizacją obiektów za pomocą synchronizacji usługi Azure AD Connect](tshoot-connect-objectsync.md)
