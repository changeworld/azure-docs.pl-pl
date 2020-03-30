---
title: Integracja z lokalnymi hasłami podczas zapisywania zwrotnego danych za pomocą narzędzia Azure AD SSPR — usługa Azure Active Directory
description: Pobierz hasła w chmurze zapisane z powrotem do lokalnej infrastruktury usługi AD
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
ms.openlocfilehash: 7fe58ae95c8d9c6b93c7e92e093541af009781ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454435"
---
# <a name="what-is-password-writeback"></a>Co to jest zapisywanie haseł?

Posiadanie narzędzia do resetowania haseł w chmurze jest świetne, ale większość firm nadal ma katalog lokalny, w którym istnieją ich użytkownicy. W jaki sposób firma Microsoft obsługuje synchronizowanie tradycyjnej lokalnej usługi Active Directory (AD) ze zmianami haseł w chmurze? Zapisywanie hasła to funkcja włączona za pomocą [usługi Azure AD Connect,](../hybrid/whatis-hybrid-identity.md) która umożliwia zapisywanie zmian haseł w chmurze w istniejącym katalogu lokalnym w czasie rzeczywistym.

Zapisywanie zwrotne hasła jest obsługiwane w środowiskach, które używają:

* [Usługi Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)
* [Synchronizacja skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Uwierzytelnianie przekazywane](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> Zapisywanie hasła przestanie działać dla klientów korzystających z usługi Azure AD Connect w wersji 1.0.8641.0 i starszych, gdy [usługa Kontrola dostępu platformy Azure (ACS) zostanie wycofana 7 listopada 2018](../azuread-dev/active-directory-acs-migration.md)r. Usługa Azure AD Connect w wersji 1.0.8641.0 i starszych nie będzie już zezwalać na zapisywanie haseł w tym czasie, ponieważ zależą one od usługi ACS dla tej funkcji.
>
> Aby uniknąć zakłóceń w działaniu usługi, uaktualnij z poprzedniej wersji usługi Azure AD Connect do nowszej wersji, zobacz artykuł [Usługi Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej](../hybrid/how-to-upgrade-previous-version.md)
>

Zapisywanie hasła zapewnia:

* **Wymuszanie lokalnych zasad haseł usługi Active Directory:** Gdy użytkownik resetuje swoje hasło, jest sprawdzany, aby upewnić się, że spełnia lokalne zasady usługi Active Directory przed zatwierdzeniem go do tego katalogu. Ta recenzja obejmuje sprawdzanie historii, złożoności, wieku, filtrów haseł i innych ograniczeń haseł zdefiniowanych w lokalnej usłudze Active Directory.
* **Zero-delay feedback**: Writeback hasło jest operacją synchronicznego. Użytkownicy są natychmiast powiadamiani, jeśli ich hasło nie spełnia zasad lub nie można ich zresetować lub zmienić z jakiegokolwiek powodu.
* **Obsługuje zmiany hasła z panelu dostępu i usługi Office 365:** Gdy zsynchronizowani użytkownicy federacyjne lub zsynchronizowane haszyszem hasła zmieniają wygasłe lub nieuczeste hasła, hasła te są zapisywane z powrotem w lokalnym środowisku usługi Active Directory.
* **Obsługuje zapisywanie haseł, gdy administrator resetuje je z witryny Azure Portal:** Gdy administrator resetuje hasło użytkownika w [witrynie Azure portal](https://portal.azure.com), jeśli ten użytkownik jest sfederowany lub hash hash zsynchronizowane, hasło jest zapisywane z powrotem do lokalnego. Ta funkcja nie jest obecnie obsługiwana w portalu administracyjnym pakietu Office.
* **Nie wymaga żadnych reguł zapory przychodzącej:** stornowania hasła używa przekaźnika usługi Azure Service Bus jako podstawowy kanał komunikacji. Cała komunikacja jest wychodząca przez port 443.

> [!NOTE]
> Konta administratora, które istnieją w grupach chronionych w lokalnej układzie AD, mogą być używane z zapisywaniem hasła. Administratorzy mogą zmieniać swoje hasło w chmurze, ale nie mogą resetować zapomnianego hasła za pomocą hasła. Aby uzyskać więcej informacji na temat chronionych grup, zobacz [Chronione konta i grupy w usłudze Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

## <a name="licensing-requirements-for-password-writeback"></a>Wymagania licencyjne dotyczące zapisywania haseł

**Samoobsługowe resetowanie/zmienianie/odblokowywanie haseł za pomocą lokalnego stornowania jest funkcją premium usługi Azure AD.** Aby uzyskać więcej informacji na temat licencjonowania, zobacz [witrynę cenową usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Aby użyć stornia hasła, musisz mieć przypisaną jedną z następujących licencji do dzierżawy:

* Usługa Azure AD — warstwa Premium P1
* Usługa Azure AD — warstwa Premium P2
* Mobilność w przedsiębiorstwie + bezpieczeństwo E3 lub A3
* Mobilność w przedsiębiorstwie + bezpieczeństwo E5 lub A5
* Microsoft 365 E3 lub A3
* Microsoft 365 E5 lub A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Autonomiczne plany licencjonowania usługi Office 365 *nie obsługują "Samoobsługowego resetowania/zmieniania/odblokowywania hasła lokalnego"* i wymagają, aby można było mieć jeden z poprzednich planów działania tej funkcji.

## <a name="how-password-writeback-works"></a>Jak działa zapisywanie zwrotne haseł

Gdy zsynchronizowany użytkownik federowany lub haszem hasło próbuje zresetować lub zmienić hasło w chmurze, występują następujące akcje:

1. Sprawdzaj, czy użytkownik ma hasło. Jeśli hasło jest zarządzane lokalnie:
   * Sprawdzaj jest wykonywana, aby sprawdzić, czy usługa writeback jest uruchomiona. Jeśli tak, użytkownik może kontynuować.
   * Jeśli usługa stornowania jest wyłączna, użytkownik jest informowany, że ich hasło nie można zresetować w tej chwili.
1. Następnie użytkownik przechodzi odpowiednie bramy uwierzytelniania i dociera do strony **Resetowanie hasła.**
1. Użytkownik wybiera nowe hasło i potwierdza je.
1. Gdy użytkownik wybierze **opcję Prześlij,** hasło w postaci zwykłego tekstu jest szyfrowane przy pomocą klucza symetrycznego utworzonego podczas procesu konfiguracji stortowania.
1. Zaszyfrowane hasło znajduje się w ładunku, który jest wysyłany za pośrednictwem kanału HTTPS do przekaźnika magistrali usług specyficzne dla dzierżawy (który jest skonfigurowany dla Ciebie podczas procesu konfiguracji stornowania). Ten przekaźnik jest chroniony losowo wygenerowanym hasłem, które zna tylko instalacja lokalna.
1. Po dotarciu do magistrali usług, punkt końcowy resetowania hasła automatycznie wznawia się i widzi, że ma żądanie resetowania oczekujących.
1. Usługa następnie wyszukuje użytkownika przy użyciu atrybutu zakotwiczenia w chmurze. Aby to wyszukiwanie zakończyło się pomyślnie:

   * Obiekt użytkownika musi istnieć w przestrzeni łącznika usługi Active Directory.
   * Obiekt użytkownika musi być połączony z odpowiednim obiektem metaverse (MV).
   * Obiekt użytkownika musi być połączony z odpowiednim obiektem łącznika usługi Azure Active Directory.
   * Łącze z obiektu łącznika usługi Active Directory do `Microsoft.InfromADUserAccountEnabled.xxx` pliku MV musi mieć regułę synchronizacji łącza.
   
   Gdy wywołanie pochodzi z chmury, aparat synchronizacji używa **atrybutu cloudAnchor** do wyszukiwania obiektu przestrzeni łącznika usługi Azure Active Directory. Następnie następuje łącze z powrotem do obiektu MV, a następnie następuje łącze z powrotem do obiektu usługi Active Directory. Ponieważ może istnieć wiele obiektów usługi Active Directory (multi-forest) dla `Microsoft.InfromADUserAccountEnabled.xxx` tego samego użytkownika, aparat synchronizacji opiera się na łączu, aby wybrać właściwy.

1. Po znalezieniu konta użytkownika podejmowana jest próba zresetowania hasła bezpośrednio w odpowiednim lesie usługi Active Directory.
1. Jeśli operacja zestawu haseł zakończy się pomyślnie, użytkownik zostanie poinformowany, że jego hasło zostało zmienione.
   > [!NOTE]
   > Jeśli skrót hasła użytkownika jest synchronizowany z usługą Azure AD przy użyciu synchronizacji skrótów haseł, istnieje prawdopodobieństwo, że lokalne zasady haseł są słabsze niż zasady haseł w chmurze. W takim przypadku zasady lokalne są wymuszane. Ta zasada gwarantuje, że zasady lokalne są wymuszane w chmurze, niezależnie od tego, czy używasz synchronizacji skrótów haseł lub federacji w celu zapewnienia logowania jednokrotnego.

1. Jeśli operacja ustawiania hasła nie powiedzie się, błąd monituje użytkownika o ponowną próbę. Operacja może zakończyć się niepowodzeniem, ponieważ:
    * Usługa została wyłączna.
    * Wybrane przez nie hasło nie spełniało zasad organizacji.
    * Nie można znaleźć użytkownika w lokalnej usłudze Active Directory.

      Komunikaty o błędach zawierają wskazówki dla użytkowników, dzięki czemu mogą próbować rozwiązać bez interwencji administratora.

## <a name="password-writeback-security"></a>Zabezpieczenia przed zapisywaniem zwrotnego hasła

Zapisywanie hasła jest wysoce bezpieczną usługą. Aby upewnić się, że informacje są chronione, czterowarstwowy model zabezpieczeń jest włączony w następujący sposób:

* **Przekaźnik magistrali usługowej specyficznej dla dzierżawcy**
   * Podczas konfigurowania usługi jest skonfigurowany przekaźnik magistrali usług specyficzne dla dzierżawy, który jest chroniony przez losowo generowane silne hasło, do którego firma Microsoft nigdy nie ma dostępu.
* **Zablokowany, kryptograficznie silny, klucz szyfrowania hasła**
   * Po utworzeniu przekaźnika magistrali usług tworzony jest silny klucz symetryczny, który jest używany do szyfrowania hasła, gdy pojawi się przez przewód. Ten klucz działa tylko w tajnym magazynie twojej firmy w chmurze, który jest mocno zablokowany i poddany audytowi, podobnie jak każde inne hasło w katalogu.
* **Standard branżowy Transport Layer Security (TLS)**
   1. Gdy operacja resetowania lub zmiany hasła występuje w chmurze, hasło w postaci zwykłego tekstu jest szyfrowane za pomocą klucza publicznego.
   1. Zaszyfrowane hasło jest umieszczane w wiadomości HTTPS wysyłanej za pośrednictwem zaszyfrowanego kanału przy użyciu certyfikatów Microsoft TLS/SSL do przekaźnika magistrali usług.
   1. Po odebraniu wiadomości w magistrali usług agenta lokalnego wznawia się i uwierzytelnia do magistrali usług przy użyciu silnego hasła, który został wcześniej wygenerowany.
   1. Agent lokalny odbiera zaszyfrowaną wiadomość i odszyfrowuje ją przy użyciu klucza prywatnego.
   1. Agent lokalny próbuje ustawić hasło za pośrednictwem interfejsu API SetPassword usługi AD DS. Ten krok umożliwia wymuszanie lokalnych zasad haseł usługi Active Directory (takich jak złożoność, wiek, historia i filtry) w chmurze.
* **Zasady wygasania wiadomości**
   * Jeśli komunikat znajduje się w magistrali usług, ponieważ usługa lokalna jest wyłączna, upływ czasu i jest usuwany po kilku minutach. Limit czasu i usunięcie wiadomości jeszcze bardziej zwiększa bezpieczeństwo.

### <a name="password-writeback-encryption-details"></a>Szczegóły szyfrowania stornadzki haseł

Po przesłaniu hasła przez użytkownika żądanie resetowania przechodzi kilka kroków szyfrowania, zanim pojawi się w środowisku lokalnym. Te kroki szyfrowania zapewniają maksymalną niezawodność i bezpieczeństwo usług. Są one opisane w następujący sposób:

* **Krok 1: Szyfrowanie hasła za pomocą 2048-bitowego klucza RSA:** Po przesłaniu hasła do ponownego zapisania w środowisku lokalnym, przesłane hasło jest szyfrowane za pomocą 2048-bitowego klucza RSA.
* **Krok 2: Szyfrowanie na poziomie pakietu z AES-GCM:** Cały pakiet, hasło i wymagane metadane, jest szyfrowany przy użyciu AES-GCM. To szyfrowanie uniemożliwia osobom mającym bezpośredni dostęp do podstawowego kanału ServiceBus przeglądanie lub manipulowanie zawartością.
* **Krok 3: Cała komunikacja odbywa się za pomocą protokołu TLS/SSL:** Cała komunikacja z ServiceBus odbywa się w kanale SSL/TLS. To szyfrowanie zabezpiecza zawartość przed nieautoryzowanymi osobami trzecimi.
* **Automatyczne przewracanie kluczy co sześć miesięcy:** wszystkie klucze są przetaczane co sześć miesięcy lub za każdym razem, gdy zapisywanie zwrotne haseł jest wyłączone, a następnie ponownie włączone w usłudze Azure AD Connect, aby zapewnić maksymalne bezpieczeństwo i bezpieczeństwo usług.

### <a name="password-writeback-bandwidth-usage"></a>Użycie przepustowości storna resetu zwrotnego hasła

Stornowania hasła to usługa o niskiej przepustowości, która wysyła żądania tylko do agenta lokalnego w następujących okolicznościach:

* Dwie wiadomości są wysyłane, gdy funkcja jest włączona lub wyłączona za pośrednictwem usługi Azure AD Connect.
* Jedna wiadomość jest wysyłana raz na pięć minut jako puls usługi tak długo, jak usługa jest uruchomiona.
* Za każdym razem, gdy zostanie przesłane nowe hasło, wysyłane są dwie wiadomości:
   * Pierwszy komunikat jest żądanie do wykonania operacji.
   * Druga wiadomość zawiera wynik operacji i jest wysyłana w następujących okolicznościach:
      * Za każdym razem, gdy nowe hasło jest przesyłane podczas samodzielnego resetowania hasła przez użytkownika.
      * Za każdym razem, gdy nowe hasło jest przesyłane podczas operacji zmiany hasła użytkownika.
      * Za każdym razem, gdy nowe hasło jest przesyłane podczas resetowania hasła użytkownika inicjowanego przez administratora (tylko z portali administracyjnych platformy Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Zagadnienia dotyczące rozmiaru i przepustowości wiadomości

Rozmiar każdej z opisanych wcześniej wiadomości jest zazwyczaj poniżej 1 KB. Nawet przy ekstremalnych obciążeniach sama usługa zapisywania zwrotnego haseł zużywa kilka kilobitów na sekundę przepustowości. Ponieważ każda wiadomość jest wysyłana w czasie rzeczywistym, tylko wtedy, gdy jest to wymagane przez operację aktualizacji hasła i ponieważ rozmiar wiadomości jest tak mały, użycie przepustowości funkcji stornia jest zbyt małe, aby mieć wymierny wpływ.

## <a name="supported-writeback-operations"></a>Obsługiwane operacje stornia zwrotnego

Hasła są zapisywane we wszystkich następujących sytuacjach:

* **Obsługiwane operacje użytkowników końcowych**
   * Każda samoobsługowa operacja dobrowolnej zmiany hasła przez użytkownika końcowego
   * Każda samoobsługowa operacja zmiany hasła przez użytkownika końcowego, na przykład wygasanie hasła
   * Resetowanie hasła przez użytkownika końcowego pochodzące z [portalu resetowania hasła](https://passwordreset.microsoftonline.com)
* **Obsługiwane operacje administratora**
   * Każda samoobsługowa operacja dobrowolnej zmiany hasła przez administratora
   * Każdy administrator samoobsługowy wymusza zmianę hasła, na przykład wygasanie hasła
   * Wszelkie samoobsługowe resetowanie hasła przez administratora pochodzące z [portalu resetowania hasła](https://passwordreset.microsoftonline.com)
   * Resetowanie hasła przez administratora z [witryny Azure Portal](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Nieobsługiwały operacje stornia

Hasła *nie* są zapisywane w żadnej z następujących sytuacji:

* **Nieobsługiwały operacje użytkownika końcowego**
   * Każdy użytkownik końcowy resetujący własne hasło przy użyciu programu PowerShell w wersji 1, 2 lub interfejsu API programu Microsoft Graph
* **Nieobsługiwały operacje administratora**
   * Resetowanie hasła użytkownika końcowego z programu PowerShell w wersji 1, 2 lub interfejsu API programu Microsoft Graph
   * Resetowanie hasła przez administratora z [centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com)

> [!WARNING]
> Użycie pola wyboru "Użytkownik musi zmienić hasło przy następnym logowaniu" w lokalnych narzędziach administracyjnych usługi Active Directory, takich jak Użytkownicy i komputery usługi Active Directory lub Centrum administracyjne usługi Active Directory, jest obsługiwane jako funkcja w wersji zapoznawczej usługi Azure AD Connect. Aby uzyskać więcej informacji, zobacz artykuł [Zaimplementowanie synchronizacji skrótów haseł z synchronizacją usługi Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Następne kroki

Włącz zapisywanie hasła za pomocą samouczka: [włączanie zapisywania haseł](tutorial-enable-writeback.md)
