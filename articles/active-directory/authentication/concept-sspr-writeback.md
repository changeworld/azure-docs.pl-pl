---
title: Integracja lokalnego zapisywania zwrotnego haseł z usługą Azure AD SSPR — Azure Active Directory
description: Uzyskiwanie haseł w chmurze z powrotem do lokalnej infrastruktury usługi AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70d3e17a007e8cfa7eed299659d4cff114f72158
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023127"
---
# <a name="what-is-password-writeback"></a>Co to jest zapisywanie zwrotne haseł?

Posiadanie narzędzia do resetowania haseł opartego na chmurze jest doskonałe, ale większość firm nadal ma katalog lokalny, w którym istnieją użytkownicy. W jaki sposób firma Microsoft obsługuje tradycyjne Active Directory lokalnych (AD) w synchronizacji ze zmianami haseł w chmurze? Funkcja zapisywania zwrotnego haseł jest funkcją włączoną [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) , która umożliwia zapisywanie zmian haseł w chmurze z powrotem do istniejącego katalogu lokalnego w czasie rzeczywistym.

Zapisywanie zwrotne haseł jest obsługiwane w środowiskach, w których są używane:

* [Usługi Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)
* [Synchronizacja skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Uwierzytelnianie przekazywane](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> Zapisywanie zwrotne haseł przestanie działać dla klientów korzystających z Azure AD Connect wersji 1.0.8641.0 i starszych, gdy [usługa Azure Access Control Service (ACS) zostanie wycofana w dniu 7 listopada, 2018](../develop/active-directory-acs-migration.md). Azure AD Connect wersje 1.0.8641.0 i starsze nie będą już zezwalały na zapisywanie zwrotne haseł, ponieważ zależą one od usług ACS dla tej funkcji.
>
> Aby uniknąć przerw w działaniu usługi, należy uaktualnić poprzednią wersję Azure AD Connect do nowszej wersji, zapoznaj się z artykułem [Azure AD Connect: uaktualnienie z poprzedniej wersji do najnowszej](../hybrid/how-to-upgrade-previous-version.md)
>

Zapisywanie zwrotne haseł zapewnia następujące informacje:

* **Wymuszanie lokalnych zasad haseł Active Directory**: gdy użytkownik resetuje swoje hasło, jest sprawdzane pod kątem zgodności lokalnych zasad Active Directory przed przekazaniem go do tego katalogu. Ten przegląd obejmuje Sprawdzanie historii, złożoności, wieku, filtrów haseł i innych ograniczeń haseł zdefiniowanych w lokalnej Active Directory.
* **Opinia o zerowej opóźnieniu**: zapisywanie zwrotne haseł jest operacją synchroniczną. Użytkownicy są powiadamiani natychmiast, jeśli ich hasło nie spełnia zasad lub nie można go zresetować ani zmienić z jakiegokolwiek powodu.
* **Obsługuje zmiany haseł z poziomu panelu dostępu i pakietu Office 365**: w przypadku, gdy użytkownicy z synchronizacją federacyjnym lub skrótem hasła mogą zmieniać wygasłe lub niewygasłe hasła, te hasła są zapisywane z powrotem do lokalnego środowiska Active Directory.
* **Obsługuje funkcję zapisywania zwrotnego haseł, gdy administrator resetuje je z Azure Portal**: za każdym razem, gdy administrator resetuje hasło użytkownika w [Azure Portal](https://portal.azure.com), jeśli ten użytkownik jest zsynchronizowany z wartością federacyjną lub skrótem hasła, hasło zostanie zapisane z powrotem do lokalnego. Ta funkcja nie jest obecnie obsługiwana w portalu administracyjnym pakietu Office.
* **Nie wymaga żadnych reguł zapory dla ruchu przychodzącego**: funkcja zapisywania zwrotnego haseł używa przekaźnika Azure Service Bus jako podstawowego kanału komunikacyjnego. Cała komunikacja jest wychodząca przez port 443.

> [!NOTE]
> Konta administratorów istniejące w grupach chronionych w lokalnej usłudze AD mogą być używane z funkcją zapisywania zwrotnego haseł. Administratorzy mogą zmienić swoje hasło w chmurze, ale nie mogą zresetować zapomnianego hasła przy użyciu resetowania hasła. Aby uzyskać więcej informacji na temat grup chronionych, zobacz [chronione konta i grupy w Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

## <a name="licensing-requirements-for-password-writeback"></a>Wymagania dotyczące licencjonowania dla zapisywania zwrotnego haseł

**Samoobsługowe resetowanie/zmiana/odblokowanie hasła przy użyciu lokalnego zapisywania zwrotnego to funkcja Premium usługi Azure AD**. Aby uzyskać więcej informacji o licencjonowaniu, zobacz [witrynę Azure Active Directory cenowej](https://azure.microsoft.com/pricing/details/active-directory/).

Aby korzystać z funkcji zapisywania zwrotnego haseł, musisz mieć jedną z następujących licencji przypisanych do dzierżawy:

* Usługa Azure AD — warstwa Premium P1
* Usługa Azure AD — warstwa Premium P2
* Enterprise Mobility + Security E3 lub A3
* Enterprise Mobility + Security E5 lub A5
* Microsoft 365 E3 lub A3
* Microsoft 365 E5 lub A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Autonomiczne plany licencjonowania pakietu Office 365 *nie obsługują funkcji samoobsługowego resetowania hasła/zmiany/odblokowywania przy użyciu lokalnego zapisywania zwrotnego* i wymagają posiadania jednego z powyższych planów, aby ta funkcja działała.

## <a name="how-password-writeback-works"></a>Jak działa zapisywanie zwrotne haseł

Gdy użytkownik z synchronizacją federacyjnego lub skrótem hasła próbuje zresetować lub zmienić hasło w chmurze, wystąpią następujące akcje:

1. Sprawdzanie jest wykonywane, aby zobaczyć, jakiego typu hasła dotyczy użytkownik. Jeśli hasło jest zarządzane lokalnie:
   * Sprawdzanie jest przeprowadzane w celu sprawdzenia, czy usługa zapisywania zwrotnego jest uruchomiona. W takim przypadku użytkownik może wykonać operację.
   * Jeśli usługa zapisywania zwrotnego nie działa, użytkownik zostanie poinformowany o tym, że nie można teraz zresetować hasła.
1. Następnie użytkownik przekazuje odpowiednie bramy uwierzytelniania i osiągnie stronę **resetowania hasła** .
1. Użytkownik wybierze nowe hasło i potwierdzi je.
1. Gdy użytkownik wybierze opcję **Prześlij**, hasło w postaci zwykłego tekstu jest szyfrowane przy użyciu klucza symetrycznego utworzonego podczas procesu konfiguracji zapisywania zwrotnego.
1. Zaszyfrowane hasło jest dołączane do ładunku, który jest wysyłany przez kanał HTTPS do przekaźnika usługi Service Bus określonego dla dzierżawy (który jest skonfigurowany dla Ciebie w procesie konfiguracji zapisywania zwrotnego). Ten przekaźnik jest chroniony przez losowo generowane hasło, które zna tylko instalacji lokalnej.
1. Gdy komunikat dociera do usługi Service Bus, punkt końcowy resetowania hasła zostanie automatycznie wznowiony i zobaczy, że ma oczekujące żądanie resetowania.
1. Następnie usługa szuka użytkownika przy użyciu atrybutu zakotwiczenia chmury. Aby to wyszukiwanie powiodło się:

   * Obiekt User musi znajdować się w przestrzeni łącznika Active Directory.
   * Obiekt użytkownika musi być połączony z odpowiednim obiektem Metaverse (MV).
   * Obiekt użytkownika musi być połączony z odpowiednim obiektem łącznika Azure Active Directory.
   * Łącze z obiektu łącznika Active Directory do MV musi mieć regułę synchronizacji `Microsoft.InfromADUserAccountEnabled.xxx` na łączu.
   
   Gdy wywołanie pochodzi z chmury, aparat synchronizacji używa atrybutu **cloudAnchor** , aby wyszukać obiekt przestrzeni łącznika Azure Active Directory. Następnie następuje po łączu z powrotem do obiektu MV, a następnie następuje po linku z powrotem do obiektu Active Directory. Ponieważ może istnieć wiele obiektów Active Directory (wiele lasów) dla tego samego użytkownika, aparat synchronizacji korzysta z linku `Microsoft.InfromADUserAccountEnabled.xxx`, aby wybrać odpowiedni.

1. Po znalezieniu konta użytkownika następuje próba zresetowania hasła bezpośrednio w odpowiednim lesie Active Directory.
1. Jeśli operacja ustawiania hasła zakończyła się pomyślnie, użytkownik otrzyma informację, że hasło zostało zmienione.
   > [!NOTE]
   > Jeśli skrót hasła użytkownika jest synchronizowany z usługą Azure AD przy użyciu funkcji synchronizacji skrótów haseł, istnieje możliwość, że lokalne zasady haseł są słabsze niż zasady haseł w chmurze. W takim przypadku zasady lokalne są wymuszane. Te zasady zapewniają, że zasady lokalne są wymuszane w chmurze, niezależnie od tego, czy w celu zapewnienia logowania jednokrotnego używasz synchronizacji skrótów haseł lub Federacji.

1. Jeśli operacja ustawiania hasła nie powiedzie się, zostanie wyświetlony komunikat o błędzie, aby spróbować ponownie. Operacja może zakończyć się niepowodzeniem, ponieważ:
    * Usługa nie działa.
    * Wybrane hasło nie spełnia zasad organizacji.
    * Nie można znaleźć użytkownika w lokalnej Active Directory.

      Komunikaty o błędach zawierają wskazówki dla użytkowników, dzięki czemu mogą próbować rozwiązać problem bez interwencji administratora.

## <a name="password-writeback-security"></a>Zabezpieczenia zapisywania zwrotnego haseł

Zapisywanie zwrotne haseł jest wysoce bezpieczną usługą. Aby zapewnić ochronę informacji, model zabezpieczeń z czterema warstwami jest włączony w następujący sposób:

* **Usługa magistrali usług specyficznych dla dzierżawy**
   * Po skonfigurowaniu usługi przekaźnik usługi Service Bus specyficzny dla dzierżawy jest skonfigurowany do ochrony za pomocą losowo wygenerowanego silnego hasła, do którego firma Microsoft nigdy nie ma dostępu.
* **Zablokowany, kryptograficznie silnie, klucz szyfrowania hasła**
   * Po utworzeniu przekaźnika usługi Service Bus zostaje utworzony silny klucz symetryczny, który jest używany do szyfrowania hasła w postaci, w jakiej się znajduje w sieci. Ten klucz znajduje się tylko w magazynie wpisów tajnych firmy w chmurze, który jest silnie zablokowany i objęty inspekcją, podobnie jak każde inne hasło w katalogu.
* **Standard branżowy Transport Layer Security (TLS)**
   1. Gdy w chmurze jest wykonywana operacja resetowania lub zmiany hasła, hasło w postaci zwykłego tekstu jest szyfrowane przy użyciu klucza publicznego.
   1. Szyfrowane hasło jest umieszczane w wiadomości HTTPS wysyłanej przez szyfrowany kanał przy użyciu certyfikatów SSL firmy Microsoft do przekaźnika usługi Service Bus.
   1. Po nadejściu wiadomości w usłudze Service Bus Agent lokalny wznawia działanie i uwierzytelnia się do usługi Service Bus przy użyciu silnego hasła, które zostało wcześniej wygenerowane.
   1. Agent lokalny odbiera zaszyfrowany komunikat i odszyfrowuje go przy użyciu klucza prywatnego.
   1. Agent lokalny próbuje ustawić hasło za pomocą interfejsu API AD DS SetPassword. Ten krok umożliwia wymuszanie Active Directory lokalnych zasad haseł (takich jak złożoność, wiek, historia i filtry) w chmurze.
* **Zasady wygasania komunikatów**
   * Jeśli komunikat znajduje się w usłudze Service Bus, ponieważ Usługa lokalna nie działa, przetrwa limit czasu i jest usuwany po kilku minutach. Limit czasu i usunięcie wiadomości zwiększają jeszcze więcej zabezpieczeń.

### <a name="password-writeback-encryption-details"></a>Szczegóły szyfrowania zapisywania zwrotnego haseł

Po przesłaniu przez użytkownika resetowania hasła żądanie resetowania przechodzi przez kilka kroków szyfrowania przed ich nadejściem do środowiska lokalnego. Te kroki szyfrowania zapewniają maksymalną niezawodność i bezpieczeństwo usługi. Są one opisane w następujący sposób:

* **Krok 1: szyfrowanie hasła przy użyciu 2048-bitowego klucza RSA**: po przesłaniu przez użytkownika hasła do zapisu z powrotem do lokalnego, przesłane hasło jest szyfrowane przy użyciu klucza RSA o 2048-bitowym.
* **Krok 2: szyfrowanie na poziomie pakietu przy użyciu algorytmu AES-GCM**: cały pakiet, hasło i wymagane metadane są szyfrowane przy użyciu algorytmu AES-GCM. To szyfrowanie uniemożliwia osobie mającej bezpośredni dostęp do bazowego kanału ServiceBus z wyświetlania lub manipulowania zawartością.
* **Krok 3. cała komunikacja odbywa się za pośrednictwem protokołu TLS/SSL**: cała komunikacja z usługą ServiceBus odbywa się w kanale SSL/TLS. To Szyfrowanie zabezpiecza zawartość przed nieautoryzowanymi stronami trzecimi.
* **Automatyczne przewinięcie kluczy co sześć miesięcy**: wszystkie klucze są wycofywane co sześć miesięcy, lub za każdym razem, gdy funkcja zapisywania zwrotnego haseł jest wyłączona, a następnie ponownie włączona w Azure AD Connect, aby zapewnić maksymalne bezpieczeństwo i bezpieczeństwo usług.

### <a name="password-writeback-bandwidth-usage"></a>Użycie przepustowości zapisywania zwrotnego haseł

Zapisywanie zwrotne haseł to usługa o niskiej przepustowości, która wysyła żądania z powrotem do lokalnego agenta w następujących okolicznościach:

* Po włączeniu lub wyłączeniu tej funkcji za pomocą Azure AD Connect są wysyłane dwa komunikaty.
* Jeden komunikat jest wysyłany co pięć minut jako puls usługi przez czas, gdy usługa jest uruchomiona.
* Za każdym razem, gdy zostanie przesłane nowe hasło, wysyłane są dwa komunikaty:
   * Pierwszy komunikat to żądanie wykonania operacji.
   * Drugi komunikat zawiera wynik operacji i jest wysyłany w następujących okolicznościach:
      * Za każdym razem, gdy w trakcie samoobsługowego resetowania hasła użytkownika zostanie przesłane nowe hasło.
      * Za każdym razem, gdy w trakcie operacji zmiany hasła użytkownika zostanie przesłane nowe hasło.
      * Za każdym razem, gdy nowe hasło zostanie przesłane podczas resetowania hasła użytkownika zainicjowanego przez administratora (tylko z portali administratora platformy Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Zagadnienia dotyczące rozmiaru i przepustowości wiadomości

Każdy opisany wcześniej komunikat ma zwykle mniej niż 1 KB. Nawet w przypadku skrajnych obciążeń sama usługa zapisywania zwrotnego haseł zużywa kilka kilobitów na sekundę przepustowości. Ponieważ każdy komunikat jest wysyłany w czasie rzeczywistym, tylko wtedy, gdy jest to wymagane przez operację aktualizacji hasła, a ponieważ rozmiar komunikatu jest mały, użycie przepustowości przez funkcję zapisywania zwrotnego jest zbyt małe i ma wymierny wpływ.

## <a name="supported-writeback-operations"></a>Obsługiwane operacje zapisywania zwrotnego

Hasła są zapisywane z powrotem we wszystkich następujących sytuacjach:

* **Obsługiwane operacje wykonywane przez użytkowników końcowych**
   * Każda operacja dobrowolnej zmiany hasła samoobsługowego użytkownika końcowego
   * Każda operacja zmiany hasła przez użytkownika końcowego, na przykład wygaśnięcie hasła
   * Wszelkie Samoobsługowe resetowanie haseł użytkowników końcowych, które pochodzą z [portalu resetowania haseł](https://passwordreset.microsoftonline.com)
* **Obsługiwane operacje administratora**
   * Każda operacja automatycznej zmiany hasła samoobsługowego administratora
   * Każda operacja zmiany hasła przez samoobsługowy administrator, na przykład wygaśnięcie hasła
   * Wszystkie Samoobsługowe resetowanie haseł administratora, które pochodzą z [portalu resetowania haseł](https://passwordreset.microsoftonline.com)
   * Wszystkie zainicjowane przez administratora Resetowanie hasła użytkownika końcowego z [Azure Portal](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Nieobsługiwane operacje zapisywania zwrotnego

Hasła *nie* są zapisywane z powrotem w żadnej z następujących sytuacji:

* **Nieobsługiwane operacje użytkownika końcowego**
   * Każdy użytkownik końcowy resetuje własne hasło przy użyciu programu PowerShell w wersji 1, wersji 2 lub interfejs API programu Graph usługi Azure AD
* **Nieobsługiwane operacje administratora**
   * Wszelkie zainicjowane przez administratora hasło użytkownika końcowego z programu PowerShell w wersji 1, wersji 2 lub interfejs API programu Graph usługi Azure AD
   * Wszystkie zainicjowane przez administratora Resetowanie hasła użytkownika końcowego w [centrum administracyjnym Microsoft 365](https://admin.microsoft.com)

> [!WARNING]
> Użycie pola wyboru "użytkownik musi zmienić hasło przy następnym logowaniu" w lokalnych Active Directory narzędziach administracyjnych, takich jak Active Directory Użytkownicy i komputery, lub Centrum administracyjne usługi Active Directory jest obsługiwana jako funkcja w wersji zapoznawczej programu Azure AD Connect. Aby uzyskać więcej informacji, zapoznaj się z artykułem [Implementacja synchronizacji skrótów haseł z synchronizacją Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md#public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon).

## <a name="next-steps"></a>Następne kroki

Włączanie zapisywania zwrotnego haseł przy użyciu samouczka: [Włączanie zapisywania zwrotnego haseł](tutorial-enable-writeback.md)
