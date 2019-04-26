---
title: W środowisku lokalnym integracji funkcji zapisywania zwrotnego haseł przy użyciu usługi Azure AD SSPR — usługi Azure Active Directory
description: Pobieranie haseł w chmurze zapisywane z powrotem do środowiska lokalnego AD infratstructure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fcf2ef10cbc8f6f54a65e596ea003a98f410a7b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60415008"
---
# <a name="what-is-password-writeback"></a>Co to jest funkcja zapisywania zwrotnego haseł?

Dobrze jest posiadanie narzędzie resetowania haseł oparte na chmurze, ale w większości firm nadal masz katalogu lokalnego, jeśli istnieją użytkownicy. Jak odbywa się przechowywanie pomocy technicznej firmy Microsoft tradycyjne lokalne Active Directory (AD) zsynchronizowany ze zmianami haseł w chmurze? Zapisywanie zwrotne haseł jest funkcją, włączana za [program Azure AD Connect](../hybrid/whatis-hybrid-identity.md) umożliwiająca zmiany haseł w chmurze, aby być zapisywane z powrotem do istniejącego katalogu lokalnego w czasie rzeczywistym.

Zapisywanie zwrotne haseł jest obsługiwane w środowiskach korzystających z:

* [Usługi Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)
* [Synchronizacja skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Uwierzytelnianie przekazywane](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> Zapisywanie zwrotne haseł przestaną działać dla klientów, którzy korzystają z wersji Azure AD Connect 1.0.8641.0 i gdy starsze [Azure Access Control service (ACS) została wycofana w dniu 7 listopada 2018](../develop/active-directory-acs-migration.md). Usługa Azure AD Connect wersje 1.0.8641.0 i starsze nie będzie już umożliwiać funkcji zapisywania zwrotnego haseł w tym czasie, ponieważ są one zależne od usługi ACS do obsługi tej funkcji.
>
> Aby uniknąć przerw w działaniu usługi, uaktualnianie z poprzedniej wersji programu Azure AD Connect do nowszej wersji, zobacz artykuł [program Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej](../hybrid/how-to-upgrade-previous-version.md)
>

Oferuje funkcję zapisywania zwrotnego haseł:

* **Wymuszanie zasad haseł usługi Active Directory w środowisku lokalnym**: Jeśli użytkownik resetuje hasła, jest sprawdzany w celu upewnij się, że spełnia ono Twoje zasady usługi Active Directory w środowisku lokalnym przed zatwierdzeniem go do tego katalogu. Ten przegląd zawiera sprawdzanie historii, złożoności, wieku, filtry hasła i inne ograniczenia haseł, które zostały zdefiniowane w lokalnej usłudze Active Directory.
* **Opóźnienie zero opinii**: Zapisywanie zwrotne haseł jest operacja synchroniczna. Użytkownicy są niezwłocznie powiadomiona, czy swoje hasło nie spełnia warunków zasady lub może nie można zresetować zmieniona z jakiegokolwiek powodu.
* **Hasło obsługuje zmieni się z panelu dostępu i usługi Office 365**: Gdy Sfederowane lub synchronizację skrótów haseł użytkownicy wrócą do zmiany haseł wygasł lub nie wygasły, te hasła są zapisywane z powrotem do środowiska lokalnego usługi Active Directory.
* **Obsługuje funkcję zapisywania zwrotnego haseł, gdy administrator resetuje je w witrynie Azure portal**: Zawsze, gdy administrator resetuje hasło użytkownika w [witryny Azure portal](https://portal.azure.com), czy ten użytkownik jest Sfederowane synchronizację skrótów haseł, hasło jest zapisywane z powrotem do środowiska lokalnego. Ta funkcja nie jest obecnie obsługiwana w portalu administracyjnym pakietu Office.
* **Nie wymaga żadnych reguł zapory dla ruchu przychodzącego**: Zapisywanie zwrotne haseł używa usługi Azure Service Bus relay jako podstawowy kanał komunikacyjny. Cała komunikacja jest wychodzących za pośrednictwem portu 443.

> [!Note]
> Nie można użyć kont użytkowników, które istnieją w ramach grup ochrony w usłudze Active Directory w środowisku lokalnym za pomocą funkcji zapisywania zwrotnego haseł. Konto administratora, które istnieją w ramach chronione grup w lokalnej usługi AD mogą być używane z zapisywaniem zwrotnym haseł. Aby uzyskać więcej informacji na temat grup ochrony, zobacz [chronione kont i grup w usłudze Active Directory](https://technet.microsoft.com/library/dn535499.aspx).
>

## <a name="licensing-requirements-for-password-writeback"></a>Wymagania licencyjne dla funkcji zapisywania zwrotnego haseł

**Samoobsługowe hasło Resetowanie/zmiana/odblokowywanie przy użyciu funkcji zapisywania zwrotnego w środowisku lokalnym jest funkcją premium usługi Azure AD**. Aby uzyskać więcej informacji na temat licencjonowania, zobacz [usługi Azure Active Directory ceny witryny](https://azure.microsoft.com/pricing/details/active-directory/).

Aby użyć funkcji zapisywania zwrotnego haseł, musi mieć jeden z następujących licencji, które są przypisane w dzierżawie usługi:

* Usługa Azure AD — warstwa Premium P1
* Usługa Azure AD — warstwa Premium P2
* Pakiet Enterprise Mobility + Security E3 lub A3
* Pakiet Enterprise Mobility + Security E5 lub A5
* Rozwiązania Microsoft 365 E3 lub A3
* Rozwiązania Microsoft 365 E5 lub A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Licencjonowanie plany usługi Office 365 autonomiczny *nie obsługują "Samoobsługi hasło Resetowanie/zmiana/odblokowywanie przy użyciu funkcji zapisywania zwrotnego w środowisku lokalnym"* i wymagają jednego z poprzednich planów dla tej funkcji do pracy.
>

## <a name="how-password-writeback-works"></a>Sposób działania funkcji zapisywania zwrotnego haseł

Skrót federacyjnego lub hasło są synchronizowane użytkownik próbuje Resetowanie lub zmienianie swoich haseł w chmurze, są wykonywane następujące akcje:

1. Sprawdzanie jest wykonywane, aby sprawdzić, jakiego rodzaju hasła użytkownika. Jeśli hasło jest zarządzane lokalnie:
   * Sprawdzanie jest wykonywane, aby zobaczyć, czy usługa zapisywania zwrotnego jest uruchomiony. Jeśli tak jest, użytkownik może kontynuować.
   * Jeśli usługa zapisywania zwrotnego nie działa, użytkownik jest informowany, że nie można zresetować swoje hasło teraz.
1. Następnie przekazuje bramy uwierzytelniania użytkownika i osiągnie **Resetuj hasło** strony.
1. Użytkownik wybiera nowe hasło i potwierdza go.
1. Gdy użytkownik wybierze **przesyłania**, przechowywała hasła są szyfrowane przy użyciu klucza symetrycznego tworzone podczas procesu instalacji funkcji zapisywania zwrotnego.
1. Zaszyfrowane hasło znajduje się w ładunku, które są przesyłane za pośrednictwem kanału protokołu HTTPS do usługi specyficznym dla dzierżawy usługi service bus relay (który jest skonfigurowany dla Ciebie podczas procesu instalacji funkcji zapisywania zwrotnego). To relay są chronione przez losowo wygenerowane hasło, który zna instalacji lokalnych.
1. Po komunikat dociera do usługi service bus, resetowania haseł punktu końcowego budzi i automatycznie widzi, że ma on oczekujące żądanie resetowania.
1. Za pomocą atrybutu zakotwiczenia chmury usługi wygląda dla użytkownika. Dla tego obszaru wyszukiwania została wykonana pomyślnie:

   * Obiekt użytkownika muszą istnieć w przestrzeni łącznika usługi Active Directory.
   * Obiekt użytkownika muszą być połączone z odpowiedniego obiektu metaverse (MV).
   * Obiekt użytkownika muszą być połączone z odpowiedni obiekt łącznika usługi Azure Active Directory.
   * Link z obiektu łącznika usługi Active Directory, aby MV musi mieć reguły synchronizacji `Microsoft.InfromADUserAccountEnabled.xxx` łącze.
   
   Po wywołaniu pochodzą z chmury, korzysta z aparatu synchronizacji **atrybutu cloudAnchor** atrybutu, aby wyszukać obiektu przestrzeni łącznika usługi Azure Active Directory. Następnie lokalizuje łącze obiektu MV, a następnie lokalizuje łącze obiektu usługi Active Directory. Ponieważ może istnieć wiele obiektów usługi Active Directory (obejmującego wiele lasów) dla tego samego użytkownika, aparat synchronizacji opiera się na `Microsoft.InfromADUserAccountEnabled.xxx` link, aby wybrać właściwy.

   > [!Note]
   > W wyniku tę logikę dla hasła funkcja zapisywania zwrotnego działała usługa Azure AD Connect musi mieć możliwość komunikowania się z emulator podstawowego kontrolera domeny (PDC). Jeśli musisz ręcznie włączyć, możesz połączyć program Azure AD Connect z emulatorem podstawowego kontrolera domeny. Kliknij prawym przyciskiem myszy **właściwości** łącznika synchronizacji usługi Active Directory, następnie wybierz pozycję **Konfigurowanie partycji katalogu**. Z tego miejsca poszukaj **ustawienia połączenia kontrolera domeny** sekcji, a następnie zaznacz pole o nazwie **korzystają z kontrolerów domeny preferowanych**. Nawet jeśli kontroler domeny preferowanych nie jest to emulator podstawowego kontrolera domeny, program Azure AD Connect próbuje nawiązać połączenie podstawowego kontrolera domeny na potrzeby zapisywania zwrotnego haseł.

1. Po użytkownik konta zostanie znaleziony, zostanie podjęta próba resetowania hasła bezpośrednio w odpowiedniej lasu usługi Active Directory.
1. W przypadku powodzenia operacji Ustaw hasło użytkownika jest informację, że ich hasło zostało zmienione.
   > [!NOTE]
   > Jeśli skrót hasła użytkownika jest zsynchronizowany z usługą Azure AD za pomocą synchronizacji skrótów haseł, istnieje prawdopodobieństwo, że w lokalnych zasadach haseł jest mniejsze niż zasady haseł w chmurze. W tym przypadku są wymuszane w lokalnych zasadach. Te zasady zapewniają, że zasady lokalne jest wymuszone w chmurze, niezależnie od tego w celu zapewnienia logowania jednokrotnego za pomocą synchronizacji skrótów haseł lub federacji.
   >

1. Jeśli hasło ustawione operacja kończy się niepowodzeniem, błąd monituje użytkownika, aby spróbować ponownie. Operacja może zakończyć się niepowodzeniem, ponieważ:
    * Usługa nie działał.
    * Hasło, które są wybrane nie spełniło zasad organizacji.
    * Nie można odnaleźć użytkownika w lokalnej usłudze Active Directory.

      Komunikaty o błędach zawierają wskazówki dla użytkowników, dzięki czemu mogą próbować rozwiązać bez interwencji administratora.

## <a name="password-writeback-security"></a>Zabezpieczeń funkcji zapisywania zwrotnego haseł

Zapisywanie zwrotne haseł jest wysoce bezpieczna usługa. Aby upewnić się, że Twoje informacje są chronione, model zabezpieczeń warstwy 4 jest włączona zgodnie z opisem w następujących czynności:

* **Specyficznym dla dzierżawy usługi service bus relay**
   * Po skonfigurowaniu usługi specyficznym dla dzierżawy usługi Service bus relay skonfigurowano chronione przez losowo generowany silne hasło, które firma Microsoft nigdy nie ma dostępu do.
* **Zablokowane, silną kryptograficznie hasło klucza szyfrowania**
   * Po utworzeniu usługi Service bus relay silnego klucza symetrycznego jest tworzony, który jest używany do szyfrowania hasła, ponieważ pochodzi przewodowo. Ten klucz znajduje się tylko w firmie magazynu wpisów tajnych w chmurze, co intensywnie jest zablokowana i inspekcji, podobnie jak każde inne hasło w katalogu.
* **Branżowy standard zabezpieczeń TLS (Transport Layer)**
   1. Gdy hasło Resetowanie lub zmienianie operacja odbywa się w chmurze, przechowywała hasła jest szyfrowana za pomocą klucza publicznego.
   1. Zaszyfrowane hasło jest umieszczany w komunikat protokołu HTTPS, który był wysyłany za pośrednictwem szyfrowanego kanału przy użyciu certyfikatów SSL firmy Microsoft do swojej usługi Service bus relay.
   1. Po odebraniu wiadomości w usłudze Service bus Twojego lokalnego agenta zostanie wznowiona i uwierzytelnia się z usługą service bus przy użyciu silnego hasła, który wcześniej został wygenerowany.
   1. Agent środowiska lokalnego przejmuje zaszyfrowanego komunikatu i odszyfrowuje ją przy użyciu klucza prywatnego.
   1. Lokalnego agenta podejmuje próbę ustawienia hasła, za pośrednictwem interfejsu API usługi AD DS SetPassword. Ten krok jest, co umożliwia wymuszanie zasad haseł lokalnej usługi Active Directory (na przykład złożoności, wieku, Historia i filtrów) w chmurze.
* **Zasady wygasania wiadomości**
   * Jeśli komunikat znajduje się w usłudze Service bus, ponieważ Twoja usługa lokalna jest wyłączona, upłynie limit czasu i zostanie usunięta po kilku minutach. Zwiększa bezpieczeństwo jeszcze bardziej, limit czasu i usuwanie wiadomości.

### <a name="password-writeback-encryption-details"></a>Szczegóły szyfrowania zapisywania zwrotnego haseł

Po użytkownik prześle resetowania haseł, żądanie zresetowania przechodzi przez kilka kroków szyfrowania przed premierą go w środowisku w środowisku lokalnym. Te kroki szyfrowania upewnij się, usługa maksymalna niezawodności i skutecznych zabezpieczeniach. Zostały one opisane w następujący sposób:

* **Krok 1. Szyfrowanie hasła przy użyciu 2048-bitowego klucza RSA**: Po przesłaniu hasła można zapisać zwrotnie w lokalnych przesłane samego hasła są szyfrowane przy użyciu 2048-bitowego klucza RSA.
* **Krok 2. Poziom pakietu szyfrowania przy użyciu usługi GCM AES**: Cały pakiet, hasło, a także wymagane metadane są szyfrowane przy użyciu usługi AES GCM. Szyfrowanie uniemożliwia każda osoba mająca bezpośredni dostęp do kanału usługi ServiceBus źródłowego wyświetlanie i manipulowanie zawartością.
* **Krok 3. Cała komunikacja odbywa się za pośrednictwem protokołu TLS/SSL**: Cała komunikacja z magistrali usług odbywa się w kanału SSL/TLS. To szyfrowanie zabezpiecza zawartości nieautoryzowanym innych firm.
* **Automatycznych kluczy przez co sześć miesięcy**: Wszystkie klucze przejdą przez co sześć miesięcy lub za każdym razem, gdy funkcji zapisywania zwrotnego haseł jest wyłączona, a następnie ponownie włączona usługa Azure AD Connect, do zapewnienia bezpieczeństwa i zabezpieczeń usługi maksymalnej.

### <a name="password-writeback-bandwidth-usage"></a>Wykorzystanie przepustowości zapisywania zwrotnego haseł

Zapisywanie zwrotne haseł jest usługą o niskiej przepustowości, która tylko wysyła żądania do lokalnego agenta w następujących okolicznościach:

* Dwa komunikaty są wysyłane, gdy ta funkcja jest włączone lub wyłączone za pomocą usługi Azure AD Connect.
* Jeden komunikat jest wysyłany po co pięć minut jako pulsu usługi, aby uzyskać, tak długo, jak usługa jest uruchomiona.
* Dwa komunikaty są wysyłane w każdym przesłaniu nowego hasła czasu:
   * Pierwszy komunikat to żądanie do wykonania tej operacji.
   * Drugi komunikat zawiera wynik operacji i są wysyłane w następujących okolicznościach:
      * Każdym przesłaniu nowego hasła podczas resetowania hasła użytkownika.
      * Każdym przesłaniu nowego hasła podczas operacji zmiany hasła użytkownika.
      * Każdym przesłaniu nowego hasła podczas użytkownika inicjowanych przez administratora resetowania hasła (tylko z portale administracyjne Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Zagadnienia dotyczące rozmiaru i przepustowości wiadomości

Rozmiar każdego komunikatu opisanych powyżej jest zwykle poniżej 1 KB. Nawet w ekstremalnych obciążeń usługi zapisywania zwrotnego haseł, sama zużywa kilka kilobitów na sekundę przepustowości. Ponieważ każdy komunikat jest wysyłany w czasie rzeczywistym, tylko wtedy, gdy jest to wymagane przez operację aktualizacji hasła, a ponieważ tak mały rozmiar komunikatu przepustowości, funkcji zapisywania zwrotnego jest zbyt mała, aby ma zauważalnego wpływu.

## <a name="supported-writeback-operations"></a>Operacje obsługiwane zapisywania zwrotnego

Hasła są zapisywane z powrotem w następujących sytuacjach:

* **Operacje obsługiwane przez użytkownika końcowego**
   * Hasło operacji zmiany dowolnego dobrowolnej samoobsługi użytkowników końcowych
   * Wszelkie życie samoobsługi użytkowników końcowych zmienić operacji hasła, na przykład, datę wygaśnięcia hasła
   * Wszelkie przez użytkownika końcowego samoobsługowego resetowania haseł pochodzący z [portal resetowania haseł](https://passwordreset.microsoftonline.com)
* **Operacje obsługiwane administratora**
   * Hasło operacji zmiany dowolnego dobrowolnej samoobsługi administratora
   * Wszelkie życie samoobsługi administratora Zmień operacji hasła, na przykład, datę wygaśnięcia hasła
   * Wszelkie administratora samoobsługowego resetowania haseł pochodzący z [portal resetowania haseł](https://passwordreset.microsoftonline.com)
   * Wszelkie inicjowanych przez administratorów użytkownik końcowy resetowania hasła z [witryny Azure portal](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Operacje zapisywania zwrotnego nieobsługiwany

Hasła są *nie* zapisywane z powrotem w następujących sytuacjach:

* **Operacje nieobsługiwane przez użytkownika końcowego**
   * Każdy użytkownik końcowy resetowania ich własnych haseł przy użyciu programu PowerShell w wersji 1, wersja 2 lub interfejsu API programu Graph usługi Azure AD
* **Nieobsługiwana administrator operacji**
   * Wszelkie inicjowanych przez administratorów użytkownik końcowy resetowania hasła z [portalu zarządzania usługi Office](https://portal.office.com)
   * Wszelkie inicjowanych przez administratorów użytkownik końcowy resetowania hasła programu PowerShell w wersji 1, wersja 2 lub interfejsu API programu Graph usługi Azure AD

## <a name="next-steps"></a>Kolejne kroki

Włącz funkcję zapisywania zwrotnego haseł przy użyciu samouczka: [Włączenie funkcji zapisywania zwrotnego haseł](tutorial-enable-writeback.md)
