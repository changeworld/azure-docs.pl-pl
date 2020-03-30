---
title: Jak bezpiecznie uruchomić usługę zarządzania certyfikatami OPC Vault — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób bezpiecznego uruchamiania usługi zarządzania certyfikatami usługi OPC Vault na platformie Azure i przegląda inne wskazówki dotyczące zabezpieczeń, które należy wziąć pod uwagę.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271709"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Bezpieczne uruchamianie usługi zarządzania certyfikatami OPC Vault

W tym artykule wyjaśniono, jak bezpiecznie uruchomić usługę zarządzania certyfikatami OPC Vault na platformie Azure, i zapoznać się z innymi wytycznymi dotyczącymi zabezpieczeń, które należy wziąć pod uwagę.

## <a name="roles"></a>Role

### <a name="trusted-and-authorized-roles"></a>Zaufane i autoryzowane role

Mikrousługa OPC Vault umożliwia różne role dostępu do różnych części usługi.

> [!IMPORTANT]
> Podczas wdrażania skrypt dodaje tylko użytkownika, który uruchamia skrypt wdrażania jako użytkownik dla wszystkich ról. W przypadku wdrożenia produkcyjnego należy przejrzeć to przypisanie roli i ponownie skonfigurować odpowiednio, postępając zgodnie z poniższymi wytycznymi. To zadanie wymaga ręcznego przypisywania ról i usług w portalu aplikacji korporacyjnych usługi Azure Active Directory (Azure AD).

### <a name="certificate-management-service-roles"></a>Role usługi zarządzania certyfikatami

Mikrousługa OPC Vault definiuje następujące role:

- **Czytnik:** Domyślnie każdy uwierzytelniony użytkownik w dzierżawie ma dostęp do odczytu. 
  - Dostęp do odczytu aplikacji i żądań certyfikatów. Może wystawiać i wyszukiwać aplikacje i żądania certyfikatów. Również informacje o odnajdowaniu urządzeń i certyfikaty publiczne są dostępne z dostępem do odczytu.
- **Moduł zapisujący**: Rola modułu zapisu jest przypisana do użytkownika w celu dodania uprawnień do zapisu dla niektórych zadań. 
  - Dostęp do odczytu/zapisu do aplikacji i żądań certyfikatów. Może rejestrować, aktualizować i wyrymazyować aplikacje. Może tworzyć żądania certyfikatów i uzyskiwać zatwierdzone klucze prywatne i certyfikaty. Można również usunąć klucze prywatne.
- **Osoba zatwierdzająca:** Rola osoby zatwierdzającej jest przypisana do użytkownika w celu zatwierdzania lub odrzucania żądań certyfikatów. Rola nie zawiera żadnej innej roli.
  - Oprócz roli osoby zatwierdzającej, aby uzyskać dostęp do interfejsu API mikrousług usługi OPC Vault, użytkownik musi mieć również uprawnienie podpisywania kluczy w usłudze Azure Key Vault, aby móc podpisywać certyfikaty.
  - Rola modułu zapisującego i zatwierdzająca powinna być przypisana do różnych użytkowników.
  - Główną rolą osoby zatwierdzającej jest zatwierdzanie generowania i odrzucania żądań certyfikatów.
- **Administrator**: Rola Administrator jest przypisana do użytkownika do zarządzania grupami certyfikatów. Rola nie obsługuje roli osoby zatwierdzającej, ale zawiera rolę pisarza.
  - Administrator może zarządzać grupami certyfikatów, zmieniać konfigurację i odwoływać certyfikaty aplikacji, wystawiając nową listę odwołania certyfikatów (CRL).
  - W idealnym przypadku role modułu zapisującego, osoby zatwierdzającej i administratora są przypisane do różnych użytkowników. Aby uzyskać dodatkowe zabezpieczenia, użytkownik z rolą Osoba zatwierdzająca lub Administrator potrzebuje również uprawnień do podpisywania kluczy w Magazynie kluczy, wystawiania certyfikatów lub odnawiania certyfikatu urzędu certyfikacji wystawcy.
  - Oprócz roli administrowania mikrousługami rola obejmuje, ale nie ogranicza się do:
    - Odpowiedzialność za administrowanie wdrażaniem praktyk bezpieczeństwa urzędu certyfikacji.
    - Zarządzanie generowaniem, odwoływaniem i zawieszaniem certyfikatów. 
    - Zarządzanie cyklem życia klucza kryptograficznego (na przykład odnowienie kluczy urzędu certyfikacji wystawcy).
    - Instalacja, konfiguracja i konserwacja usług obsługujących urząd certyfikacji.
    - Codzienne działanie usług. 
    - Ca i kopii zapasowej bazy danych i odzyskiwania.

### <a name="other-role-assignments"></a>Inne przypisania ról

Należy również wziąć pod uwagę następujące role podczas uruchamiania usługi:

- Właściciel firmy zamówienia na zamówienie z zewnętrznym głównym urzędem certyfikacji (na przykład, gdy właściciel kupuje certyfikaty od zewnętrznego urzędu certyfikacji lub obsługuje urząd certyfikacji podrzędny, który jest podrzędny od zewnętrznego urzędu certyfikacji).
- Opracowanie i walidacja urzędu certyfikacji.
- Przegląd dokumentacji inspekcji.
- Pracownicy, którzy pomagają w obsłudze urzędu certyfikacji lub zarządzają obiektami fizycznymi i chmurowymi, ale nie są bezpośrednio zaufani do wykonywania operacji urzędu certyfikacji, *pełnią rolę autoryzowaną.* Zestaw zadań, które osoby w roli autoryzowanej mogą wykonywać, musi być również udokumentowany.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Przeglądanie członkostw zaufanych i autoryzowanych ról kwartalnie

Przeglądanie członkostwa zaufanych i autoryzowanych ról co najmniej raz na kwartał. Upewnij się, że zestaw osób (dla procesów ręcznych) lub tożsamości usługi (dla zautomatyzowanych procesów) w każdej roli jest ograniczona do minimum.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Oddzielenie roli między żądaczem certyfikatu a osoba zatwierdzająca

Proces wydawania certyfikatów musi wymuszać oddzielenie roli między rolą żądacza certyfikatów a rolą osoby zatwierdzającej certyfikat (osoby lub systemy zautomatyzowane). Wydawanie certyfikatów musi być autoryzowane przez rolę osoby zatwierdzającej certyfikat, która sprawdza, czy żądacz certyfikatu jest upoważniony do uzyskiwania certyfikatów. Osoby, które pełnią rolę osoby zatwierdzającej certyfikat, muszą być osobą formalnie upoważnioną.

### <a name="restrict-assignment-of-privileged-roles"></a>Ograniczanie przypisywania ról uprzywilejowanych

Należy ograniczyć przypisywanie ról uprzywilejowanych, takich jak autoryzowanie członkostwa w grupie Administratorzy i osoby zatwierdzające, do ograniczonego zestawu autoryzowanych pracowników. Wszelkie zmiany ról uprzywilejowanych muszą mieć dostęp odwołany w ciągu 24 godzin. Na koniec przejrzyj przypisania ról uprzywilejowanych co kwartał i usuń niepotrzebne lub wygasłe przypisania.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Role uprzywilejowane powinny używać uwierzytelniania dwuskładnikowego

Użyj uwierzytelniania wieloskładnikowego (nazywanego również uwierzytelnianiem dwuskładnikowym) w przypadku interaktywnych logów osób zatwierdzających i administratorów w usłudze.

## <a name="certificate-service-operation-guidelines"></a>Wskazówki dotyczące obsługi usług certyfikatów

### <a name="operational-contacts"></a>Kontakty operacyjne

Usługa certyfikatów musi mieć w pliku aktualny plan odpowiedzi na zabezpieczenia, który zawiera szczegółowe kontakty operacyjne dotyczące reagowania na incydenty.

### <a name="security-updates"></a>Aktualizacje zabezpieczeń

Wszystkie systemy muszą być stale monitorowane i aktualizowane za pomocą najnowszych aktualizacji zabezpieczeń.

> [!IMPORTANT]
> Repozytorium GitHub usługi OPC Vault jest stale aktualizowane za pomocą poprawek zabezpieczeń. Monitoruj te aktualizacje i stosuj je do usługi w regularnych odstępach czasu.

### <a name="security-monitoring"></a>Monitorowanie zabezpieczeń

Subskrybować lub wdrożyć odpowiednie monitorowanie zabezpieczeń. Na przykład subskrybuj centralne rozwiązanie do monitorowania (takie jak Usługa Azure Security Center lub rozwiązanie do monitorowania usługi Office 365) i skonfiguruj go odpowiednio, aby upewnić się, że zdarzenia zabezpieczeń są przesyłane do rozwiązania do monitorowania.

> [!IMPORTANT]
> Domyślnie usługa OPC Vault jest wdrażana z [usługą Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) jako rozwiązanie do monitorowania. Zaleca się dodanie rozwiązania zabezpieczeń, takiego jak [Usługa Azure Security Center.](https://azure.microsoft.com/services/security-center/)

### <a name="assess-the-security-of-open-source-software-components"></a>Ocena bezpieczeństwa komponentów oprogramowania typu open source

Wszystkie składniki typu open source używane w ramach produktu lub usługi muszą być wolne od umiarkowanych lub większych luk w zabezpieczeniach.

> [!IMPORTANT]
> Podczas ciągłej integracji kompilacje repozytorium GitHub usługi OPC Vault skanuje wszystkie składniki w poszukiwaniu luk w zabezpieczeniach. Monitoruj te aktualizacje w usłudze GitHub i stosuj je do usługi w regularnych odstępach czasu.

### <a name="maintain-an-inventory"></a>Obsługa zapasów

Obsługa zapasów zasobów dla wszystkich hostów produkcyjnych (w tym trwałych maszyn wirtualnych), urządzeń, wszystkich wewnętrznych zakresów adresów IP, adresów VIP i nazw domen publicznej DNS. Za każdym razem, gdy dodajesz lub usuwasz system, adres IP urządzenia, adres VIP lub publiczną domenę DNS, musisz zaktualizować spis w ciągu 30 dni.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Spis domyślnego wdrożenia produkcji mikrousług usługi Azure OPC Vault 

Na platformie Azure:
- **Plan usługi aplikacji:** plan usługi aplikacji dla hostów usług. Domyślna wartość S1.
- **Usługa aplikacji** dla mikrousług: host usługi OPC Vault.
- **Usługa app dla** przykładowej aplikacji: przykładowy host aplikacji OPC Vault.
- **Standard usługi Key Vault:** Do przechowywania wpisów tajnych i kluczy usługi Azure Cosmos DB dla usług sieci web.
- **Key Vault Premium**: Do obsługi kluczy urzędu certyfikacji wystawcy, do podpisywania usługi oraz do konfiguracji magazynu i przechowywania kluczy prywatnych aplikacji.
- **Usługa Azure Cosmos DB:** Baza danych dla żądań aplikacji i certyfikatów. 
- **Usługa Application Insights:**(opcjonalnie) Rozwiązanie do monitorowania usługi sieci web i aplikacji.
- **Rejestracja aplikacji usługi Azure AD:** rejestracja dla przykładowej aplikacji, usługi i modułu brzegowego.

W przypadku usług w chmurze wszystkie nazwy hostów, grupy zasobów, nazwy zasobów, identyfikatory subskrypcji i identyfikatory dzierżawy używane do wdrażania usługi powinny być udokumentowane. 

W usłudze Azure IoT Edge lub lokalnym serwerze usługi IoT Edge:
- **Moduł OPC Vault IoT Edge**: Do obsługi sieci fabrycznej OPC UA Global Discovery Server. 

W przypadku urządzeń usługi IoT Edge należy udokumentować nazwy hostów i adresy IP. 

### <a name="document-the-certification-authorities-cas"></a>Dokumentują urzędy certyfikacji

Dokumentacja hierarchii urzędu certyfikacji musi zawierać wszystkie obsługiwane urzędy certyfikacji. Obejmuje to wszystkie powiązane podrzędne urzędy certyfikacji, nadrzędne urzędy certyfikacji i główne urzędy certyfikacji, nawet jeśli nie są zarządzane przez usługę. Zamiast formalnej dokumentacji można podać wyczerpujący zestaw wszystkich nieuczyszczonych certyfikatów urzędu certyfikacji.

> [!NOTE]
> Przykładowa aplikacja OPC Vault obsługuje pobieranie wszystkich certyfikatów używanych i produkowanych w usłudze dokumentacji.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Dokumentowanie wystawionych certyfikatów przez wszystkie urzędy certyfikacji

Podaj wyczerpujący zestaw wszystkich certyfikatów wydanych w ciągu ostatnich 12 miesięcy.

> [!NOTE]
> Przykładowa aplikacja OPC Vault obsługuje pobieranie wszystkich certyfikatów używanych i produkowanych w usłudze dokumentacji.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>Dokumentowanie standardowej procedury operacyjnej bezpiecznego usuwania kluczy kryptograficznych

W okresie istnienia urzędu certyfikacji usunięcie klucza może nastąpić rzadko. Dlatego żaden użytkownik nie ma przypisanego prawa usuwania certyfikatu usługi Key Vault i dlaczego nie ma żadnych interfejsów API narażonych na usunięcie certyfikatu urzędu certyfikacji wystawcy. Ręczna standardowa procedura obsługi bezpiecznego usuwania kluczy kryptograficznych urzędu certyfikacji jest dostępna tylko przez bezpośredni dostęp do usługi Key Vault w witrynie Azure portal. Można również usunąć grupę certyfikatów w magazynie kluczy. Aby zapewnić natychmiastowe usunięcie, wyłącz funkcję [usuwania nietrwałego w magazynie kluczy.](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

## <a name="certificates"></a>Certyfikaty

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Certyfikaty muszą być zgodne z minimalnym profilem certyfikatu

Usługa OPC Vault to internetowy urząd certyfikacji, który wystawia certyfikaty jednostek końcowych subskrybentom. Mikrousługa OPC Vault jest zgodna z tymi wytycznymi w implementacji domyślnej.

- Wszystkie certyfikaty muszą zawierać następujące pola X.509, jak określono poniżej:
  - Zawartość pola wersji musi być w wersji 3. 
  - Zawartość pola numer seryjny musi zawierać co najmniej 8 bajtów entropii uzyskanej z zatwierdzonego generatora liczb losowych FIPS (Federal Information Processing Standards) 140.<br>
    > [!IMPORTANT]
    > Numer seryjny OPC Vault jest domyślnie 20 bajtów i jest uzyskiwany z systemu operacyjnego kryptograficzne generatora liczb losowych. Generator liczb losowych jest zatwierdzony przez FIPS 140 na urządzeniach z systemem Windows, ale nie na Linuksie. Należy wziąć to pod uwagę przy wyborze wdrożenia usługi, która używa maszyn wirtualnych z systemem Linux lub kontenerów dokowane Linux, na których podstawowa technologia OpenSSL nie jest zatwierdzony FIPS 140.
  - Nie może być obecny identyfikator wystawcyUniqueID i subjectUniqueID.
  - Certyfikaty jednostek końcowych muszą być identyfikowane z rozszerzeniem podstawowych ograniczeń, zgodnie z RFC 5280 IETF.
  - Pole pathLenConstraint musi być ustawione na 0 dla certyfikatu Wystawianie urzędu certyfikacji. 
  - Rozszerzenie rozszerzonego użycia klucza musi być obecne i musi zawierać minimalny zestaw identyfikatorów obiektów rozszerzonego użycia klucza (ED). Nie można określić dowolnego identyfikatora oidextendedkeyusage (2.5.29.37.0). 
  - Rozszerzenie punktu dystrybucji programu CRL (CDP) musi znajdować się w certyfikacie urzędu certyfikacji wystawcy.<br>
    > [!IMPORTANT]
    > Rozszerzenie CDP jest obecne w certyfikatach OPC Vault CA. Niemniej jednak urządzenia OPC UA używają niestandardowych metod do dystrybucji list CRL.
  - Rozszerzenie dostępu do informacji urzędu musi być obecne w certyfikatach subskrybenta.<br>
    > [!IMPORTANT]
    > Rozszerzenie dostępu do informacji urzędu jest obecne w certyfikatach subskrybentów OPC Vault. Niemniej jednak urządzenia OPC UA używają niestandardowych metod do rozpowszechniania informacji o ucho.
- Należy użyć zatwierdzonych algorytmów asymetrycznych, długości kluczy, funkcji mieszania i trybów dopełnienia.
  - RSA i SHA-2 są jedynymi obsługiwanymi algorytmami.
  - RSA może być używany do szyfrowania, wymiany kluczy i podpisu.
  - Szyfrowanie RSA musi używać tylko trybów dopełnienia OAEP, RSA-KEM lub RSA-PSS.
  - Wymagane są długości klucza większe lub równe 2048 bitom.
  - Użyj rodziny algorytmów mieszania SHA-2 (SHA256, SHA384 i SHA512).
  - Klucze głównego urzędu certyfikacji RSA o typowym okresie istnienia większym lub równym 20 lat muszą mieć wartość 4096 bitów lub więcej.
  - Klucze urzędu certyfikacji wystawcy RSA muszą mieć co najmniej 2048 bitów. Jeśli data wygaśnięcia certyfikatu urzędu certyfikacji przypada po 2030 r., klucz urzędu certyfikacji musi mieć 4096 bitów lub więcej.
- Okres istnienia certyfikatu
  - Certyfikaty głównego urzędu certyfikacji: Maksymalny okres ważności certyfikatu dla głównych urzędów certyfikacji nie może przekraczać 25 lat.
  - Sub CA lub online certyfikaty urzędu certyfikacji wystawcy: Maksymalny okres ważności certyfikatu dla urzędów certyfikacji, które są w trybie online i wystawiają tylko certyfikaty subskrybentów, nie może przekraczać 6 lat. W przypadku tych certyfikatów nie można używać powiązanego klucza podpisu prywatnego dłużej niż 3 lata do wystawiania nowych certyfikatów.<br>
    > [!IMPORTANT]
    > Certyfikat wystawcy, wygenerowany w domyślnej mikrousługi OPC Vault bez zewnętrznego głównego urzędu certyfikacji, jest traktowany jak subuczłony urząd certyfikacji online, z odpowiednimi wymaganiami i okresami istnienia. Domyślny okres istnienia jest ustawiony na 5 lat, przy długości klucza większej lub równej 2048.
  - Wszystkie klucze asymetryczne muszą mieć maksymalnie 5-letni okres istnienia i zalecany okres istnienia 1 roku.<br>
    > [!IMPORTANT]
    > Domyślnie okresy istnienia certyfikatów aplikacji wystawionych w OPC Vault mają okres istnienia 2 lat i powinny być wymieniane co roku. 
  - Za każdym razem, gdy certyfikat jest odnawiany, jest odnawiany za pomocą nowego klucza.
- Rozszerzenia specyficzne dla OPC UA w certyfikatach wystąpień aplikacji
  - Rozszerzenie subjectAltName zawiera aplikację Uri i nazwy hostów. Mogą one również obejmować adresy FQDN, IPv4 i IPv6.
  - KeyUsage obejmuje digitalSignature, nonRepudiation, keyEncipherment i dataEncipherment.
  - ExtendedKeyUsage zawiera serverAuth i clientAuth.
  - Identyfikator certyfikatu authorityKeyIdentifier jest określony w podpisanych certyfikatach.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>Klucze urzędu certyfikacji i certyfikaty muszą spełniać minimalne wymagania

- **Klucze prywatne:** klucze RSA muszą mieć co najmniej 2048 bitów. Jeśli data wygaśnięcia certyfikatu urzędu certyfikacji przypada po 2030 r., klucz urzędu certyfikacji musi mieć 4096 bitów lub więcej.
- **Okres istnienia:** Maksymalny okres ważności certyfikatu dla urzędów certyfikacji, które są w trybie online i wystawiają tylko certyfikaty subskrybenta, nie może przekraczać 6 lat. W przypadku tych certyfikatów nie można używać powiązanego klucza podpisu prywatnego dłużej niż 3 lata do wystawiania nowych certyfikatów.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>Klucze urzędu certyfikacji są chronione za pomocą sprzętowych modułów zabezpieczeń

OpcVault używa usługi Azure Key Vault Premium, a klucze są chronione przez sprzętowe moduły zabezpieczeń (HSM) fips 140-2 poziomu 2. 

Moduły kryptograficzne używane przez program Key Vault, niezależnie od tego, czy jest modułem HSM, czy oprogramowaniem, są sprawdzane fips. Klucze utworzone lub zaimportowane jako chronione przez moduł HSM są przetwarzane wewnątrz modułu HSM, sprawdzane zgodnie z fips 140-2 Poziom 2. Klucze utworzone lub zaimportowane jako chronione programem są przetwarzane wewnątrz modułów kryptograficznych zweryfikowanych zgodnie z fips 140-2 Poziom 1.

## <a name="operational-practices"></a>Praktyki operacyjne

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Dokumentowanie i obsługa standardowych praktyk operacyjnych infrastruktury kluczy publicznych podczas rejestrowania certyfikatów

Dokumentowanie i obsługa standardowych procedur operacyjnych (SO) dotyczących sposobu wystawiania certyfikatów przez służby certyfikacji, w tym: 
- Sposób identyfikacji i uwierzytelnionego subskrybenta. 
- Sposób przetwarzania i sprawdzania poprawności żądania certyfikatu (w stosownych przypadkach obejmują również sposób przetwarzania żądań odnowienia certyfikatu i ponownego klucza). 
- Sposób dystrybucji wystawionych certyfikatów do subskrybentów. 

SOP mikrousługi OPC Vault jest opisany w [architekturze OPC Vault](overview-opc-vault-architecture.md) i [zarządzaj usługą certyfikatów OPC Vault](howto-opc-vault-manage.md). Praktyki są zgodne z "OPC Unified Architecture Specification Part 12: Discovery and Global Services".


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Dokumentowanie i obsługa standardowych praktyk operacyjnych infrastruktury kluczy publicznych w celu odwołania certyfikatów

Proces odwoływania certyfikatów jest opisany w [architekturze OPC Vault](overview-opc-vault-architecture.md) i [zarządzaj usługą certyfikatów OPC Vault](howto-opc-vault-manage.md).
    
### <a name="document-ca-key-generation-ceremony"></a>Ceremonia generowania klucza urzędu certyfikacji dokumentu 

Generowanie klucza urzędu certyfikacji wystawcy w mikrousługach OPC Vault jest uproszczone ze względu na bezpieczną pamięć masową w usłudze Azure Key Vault. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą certyfikatów OPC Vault](howto-opc-vault-manage.md).

Jednak podczas korzystania z zewnętrznego głównego urzędu certyfikacji ceremonia generowania klucza urzędu certyfikacji musi spełniać następujące wymagania.

Ceremonia generowania klucza urzędu certyfikacji musi być wykonana względem udokumentowanego skryptu, który zawiera co najmniej następujące elementy: 
- Definicja ról i obowiązków uczestników.
- Zatwierdzenie do przeprowadzenia ceremonii generowania klucza urzędu certyfikacji.
- Sprzęt kryptograficzny i materiały aktywacyjne wymagane do ceremonii.
- Przygotowanie sprzętu (w tym aktualizacja informacji o zasobie/konfiguracji i wylogowanie).
- Instalacja systemu operacyjnego.
- Określone kroki wykonywane podczas ceremonii generowania klucza urzędu certyfikacji, takie jak: 
  - Instalacja i konfiguracja aplikacji urzędu certyfikacji.
  - Generowanie klucza urzędu certyfikacji.
  - Kopia zapasowa klucza urzędu certyfikacji.
  - Podpisywanie certyfikatów urzędu certyfikacji.
  - Importowanie podpisanych kluczy w chronionym modułie HSM usługi.
  - Zamknięcie systemu urzędu certyfikacji.
  - Przygotowanie materiałów do przechowywania.


## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak bezpiecznie zarządzać OPC Vault, możesz:

> [!div class="nextstepaction"]
> [Bezpieczne urządzenia OPC UA z OPC Vault](howto-opc-vault-secure.md)
