---
title: Jak bezpiecznie uruchomić usługę zarządzania certyfikatami magazynu OPC — platforma Azure | Microsoft Docs
description: Opisuje sposób bezpiecznego uruchamiania usługi zarządzania certyfikatami magazynu OPC na platformie Azure i innych wytycznych dotyczących zabezpieczeń, które należy wziąć pod uwagę.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b08358680793ccdadca27c5f2aa57fbffe89b53a
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973794"
---
# <a name="how-to-run-the-opc-ua-certificate-management-service-securely"></a>Jak bezpiecznie uruchomić usługę zarządzania certyfikatami OPC UA

W tym artykule opisano sposób bezpiecznego uruchamiania usługi zarządzania certyfikatami OPC UA na platformie Azure oraz innych wytycznych dotyczących zabezpieczeń, które należy wziąć pod uwagę.

## <a name="roles"></a>Role

### <a name="trusted-and-authorized-roles"></a>Zaufane i autoryzowane role

Mikrousługa magazynu OPC jest skonfigurowana tak, aby zezwalać na różne role do uzyskiwania dostępu do różnych części usługi.

> [!IMPORTANT]
> Podczas wdrażania skrypt dodaje tylko użytkownika, który uruchamia skrypt wdrożenia jako użytkownik dla wszystkich ról.
> To przypisanie roli należy przejrzeć pod kątem wdrożenia produkcyjnego i ponownie skonfigurować odpowiednio zgodnie z poniższymi wskazówkami.
> To zadanie wymaga ręcznego przypisania ról i usług w portalu aplikacji usługi Azure AD Enterprise.

### <a name="certificate-management-service-roles"></a>Role usługi zarządzania certyfikatami

Mikrousługa definiuje następujące role:

- **Czytelnik**: Domyślnie każdy uwierzytelniony użytkownik w dzierżawie ma dostęp do odczytu. 
  - Dostęp do odczytu do aplikacji i żądań certyfikatów. Może wyświetlać listę aplikacji i żądań certyfikatów oraz wykonywać dla nich zapytania. Również informacje o odnajdywaniu urządzeń i certyfikaty publiczne są dostępne z dostępem do odczytu.
- **Składnik zapisywania**: Rola składnika zapisywania zostaje przypisana do użytkownika w celu dodania uprawnień do zapisu dla określonych zadań. 
  - Dostęp do odczytu i zapisu do aplikacji i żądań certyfikatów. Może rejestrować, aktualizować i wyrejestrować aplikacje. Może tworzyć żądania certyfikatów i uzyskiwać zatwierdzone klucze prywatne i certyfikaty. Można również usunąć klucze prywatne.
- **Osoba zatwierdzająca**: Rola osoby zatwierdzającej jest przypisana do użytkownika w celu zatwierdzenia lub odrzucenia żądań certyfikatów. Rola nie obejmuje żadnej innej roli.
  - Oprócz roli osoby zatwierdzającej w celu uzyskania dostępu do interfejsu API mikrousług magazynu OPC użytkownik musi mieć również uprawnienie do podpisywania klucza w Key Vault, aby podpisać certyfikaty.
  - Rola składnika zapisywania i osoby zatwierdzającej powinna być przypisana do różnych użytkowników.
  - Główną rolą osoby zatwierdzającej jest zatwierdzenie generacji i odrzucenie żądań certyfikatów.
- **Administrator**: Rola Administrator jest przypisana do użytkownika w celu zarządzania grupami certyfikatów. Rola nie obsługuje roli osoby zatwierdzającej, ale obejmuje rolę składnika zapisywania.
  - Administrator może zarządzać grupami certyfikatów, zmieniać konfigurację i odwołać certyfikaty aplikacji, wydając nową listę CRL.
  - W idealnym przypadku role modułu zapisywania, osoby zatwierdzającej i administratora są przypisywane do różnych użytkowników. Aby uzyskać dodatkowe zabezpieczenia, użytkownik z osobą zatwierdzającą lub rolą administratora musi również mieć uprawnienie do podpisywania klucza w magazynie kluczy, aby wystawiać certyfikaty lub odnowić certyfikat urzędu wystawcy.
  - Oprócz roli administratora mikrousług rola obejmuje również, ale nie ogranicza się do:
    - Odpowiedzialny za administrowanie wdrażaniem praktyk w zakresie zabezpieczeń urzędu certyfikacji.
    - Zarządzanie generowaniem, odwoływaniem i zawieszeniem certyfikatów. 
    - Zarządzanie cyklem życia kluczy kryptograficznych (na przykład odnowienie kluczy urzędu certyfikacji wystawcy).
    - Instalacja, konfiguracja i konserwacja usług, które obsługują urząd certyfikacji.
    - Codzienne operacje na usługach. 
    - Kopia zapasowa i odzyskiwanie bazy danych.

### <a name="other-role-assignments"></a>Inne przypisania ról

Podczas uruchamiania usługi należy również uwzględnić i przypisać następujące role:

- Właściciel kontraktu zakupu certyfikatu z zewnętrznym głównym urzędem certyfikacji (w przypadku, gdy właściciel kupuje certyfikaty od zewnętrznego urzędu certyfikacji lub prowadzi urząd certyfikacji, który jest podrzędny wobec zewnętrznego urzędu certyfikacji).
- Opracowywanie i sprawdzanie poprawności urzędu certyfikacji.
- Przegląd rekordów inspekcji.
- Personel, który pomaga w obsłudze urzędu certyfikacji lub zarządzania urządzeniami fizycznymi i w chmurze, ale nie jest bezpośrednio zaufany w celu wykonywania operacji urzędu certyfikacji jest zdefiniowany jako autoryzowana rola. Aby można było wykonać zestaw zadań w autoryzowanej roli, należy również udokumentować.

### <a name="memberships-of-trusted-and-authorized-roles-must-be-reviewed-annually"></a>Członkostwa zaufanych i autoryzowanych ról muszą być analizowane co rok

Członkostwo zaufanych i autoryzowanych ról musi być analizowane co najmniej kwartalnie, aby upewnić się, że zestaw osób (dla procesów ręcznych) lub tożsamości usług (dla zautomatyzowanych procesów) w każdej roli jest minimalny.

### <a name="certificate-issuance-process-must-enforce-role-separation-between-certificate-requester-and-approver"></a>Proces wystawiania certyfikatów musi wymusić separację ról między żądającym certyfikatu a osobą zatwierdzającą

Proces wystawiania certyfikatów musi wymusić rozdzielenie ról między rolami żądającymi certyfikatu a zaakceptowaniem certyfikatu (osobami lub systemami zautomatyzowanymi). Wystawianie certyfikatów musi być autoryzowane przez rolę osoby zatwierdzającej certyfikat, która sprawdza, czy osoba żądająca certyfikatu ma autoryzację do uzyskiwania certyfikatów. Osoby, które posiadają rolę osoby zatwierdzającej certyfikat, muszą być osobą autoryzowaną w formie formalnie.

### <a name="privileged-role-management-must-restrict-access-and-be-reviewed-quarterly"></a>Zarządzanie rolami uprzywilejowanymi musi ograniczyć dostęp i być przeglądane co kwartał

Przypisanie ról uprzywilejowanych, takich jak autoryzacja członkostwa w grupie Administratorzy i osoby zatwierdzające, musi być ograniczone do ograniczonego zestawu autoryzowanego personelu. Wszelkie zmiany ról uprzywilejowanych muszą mieć dostęp odwołany w ciągu 24 godzin. Na koniec przydziały ról uprzywilejowanych muszą być przeglądane co kwartał i wszystkie niepotrzebne lub wygasłe przypisania muszą zostać usunięte.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Role uprzywilejowane powinny korzystać z uwierzytelniania dwuskładnikowego.

Uwierzytelnianie wieloskładnikowe (uwierzytelnianie dwuskładnikowe, MFA lub TFA) musi być używane do logowania interakcyjnego osób zatwierdzających i administratorów do usługi.

## <a name="certificate-service-operation-guidelines"></a>Wytyczne dotyczące operacji usługi certyfikatów

### <a name="operational-contacts"></a>Kontakty operacyjne

Usługa certyfikatów musi mieć aktualny plan odpowiedzi na zabezpieczenia w pliku, który zawiera szczegółowe kontakty dotyczące odpowiedzi na zdarzenia operacyjne.

### <a name="security-updates"></a>Aktualizacje zabezpieczeń

Wszystkie systemy muszą być stale monitorowane i aktualizowane przy użyciu najnowszych aktualizacji zabezpieczeń/zgodności poprawek.

> [!IMPORTANT]
> Repozytorium GitHub usługi magazynu OPC jest stale aktualizowana przy użyciu poprawek zabezpieczeń. Aktualizacje w serwisie GitHub powinny być monitorowane i aktualizacje mają być stosowane do usługi w regularnych odstępach czasu.

### <a name="security-monitoring"></a>Monitorowanie zabezpieczeń

Zasubskrybuj lub Zaimplementuj odpowiednie monitorowanie zabezpieczeń, np. subskrybując centralne rozwiązanie do monitorowania (na przykład Azure Security Center, rozwiązanie do monitorowania usługi O365) i skonfiguruj je odpowiednio w celu zapewnienia przekazywania zdarzeń zabezpieczeń do monitorowanie rozwiązania.

> [!IMPORTANT]
> Domyślnie usługa magazynu OPC jest wdrażana za pomocą [Application Insights platformy Azure](https://docs.microsoft.com/azure/azure-monitor/app/devops) jako rozwiązania do monitorowania. Dodawanie rozwiązania zabezpieczeń, takiego jak [Azure Security Center](https://azure.microsoft.com/services/security-center/) , jest zdecydowanie zalecane.

### <a name="assess-security-of-open-source-software-components"></a>Ocena zabezpieczeń składników oprogramowania typu open source

Wszystkie składniki typu "open source" używane w ramach produktu lub usługi muszą być wolne od średnich lub większej liczby luk w zabezpieczeniach.

> [!IMPORTANT]
> Repozytorium GitHub usługi magazynu OPC skanuje wszystkie składniki podczas kompilacji ciągłej integracji pod kątem luk w zabezpieczeniach. Aktualizacje w serwisie GitHub powinny być monitorowane i aktualizacje mają być stosowane do usługi w regularnych odstępach czasu.

### <a name="maintain-an-inventory"></a>Obsługa spisu

Należy zachować spis zasobów dla wszystkich hostów produkcyjnych (w tym trwałych maszyn wirtualnych), urządzeń, wszystkich wewnętrznych zakresów adresów IP, adresów VIP i publicznych nazw domen DNS. Ten spis należy zaktualizować z dodaniem lub usunięciem systemu, adresu IP urządzenia, adresów VIP lub publicznej domeny DNS w ciągu 30 dni.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Spis domyślnego wdrożenia produkcyjnego mikrousługi magazynu Azure OPC: 

Na **platformie Azure**:
- **Plan App Service**: Plan usługi App Service dla hostów usług. Domyślna wartość S1.
- **App Service** mikrousługi: Host usługi magazynu OPC.
- **App Service** dla przykładowej aplikacji: Host przykładowej aplikacji magazynu OPC.
- **Standard magazynu**kluczy: Przechowywanie wpisów tajnych i kluczy Cosmos DB dla usług sieci Web.
- **Premium magazynu**kluczy: Do hostowania kluczy urzędu certyfikacji wystawcy dla usługi podpisywania w celu skonfigurowania magazynu i magazynowania kluczy prywatnych aplikacji.
- **Cosmos DB**: Baza danych dla żądań aplikacji i certyfikatów. 
- **Application Insights**: (opcjonalnie) monitorowanie rozwiązania dla usług i aplikacji sieci Web.
- **AzureAD rejestracji aplikacji**: Rejestracja dla przykładowej aplikacji, usługi i modułu brzegowego.

W przypadku usług Cloud Services wszystkie nazwy hostów, grupy zasobów, nazwy zasobów, Identyfikator subskrypcji, TenantId używany do wdrożenia usługi powinny być udokumentowane. 

W **IoT Edge** lub na lokalnym **serwerze IoT Edge**:
- **Moduł IoT Edge magazynu OPC**: Aby zapewnić obsługę globalnego serwera odnajdywania OPC UA programu Factory Network. 

W przypadku urządzeń IoT Edge nazwy hostów i adresów IP powinny być udokumentowane. 

### <a name="document-the-certification-authorities-cas"></a>Udokumentowanie urzędów certyfikacji

Dokumentacja hierarchii urzędu certyfikacji musi zawierać wszystkie obsługiwane urzędy certyfikacji, w tym wszystkie powiązane podrzędne urzędy certyfikacji, nadrzędne urzędy certyfikacji i główne urzędy certyfikacji, nawet jeśli nie są zarządzane przez usługę. Można uzyskać wyczerpujący zestaw wszystkich niewygasłych certyfikatów urzędu certyfikacji, zamiast formalnej dokumentacji.

> [!IMPORTANT]
> Przykładowa aplikacja magazynu OPC obsługuje pobieranie wszystkich certyfikatów używanych i wygenerowanych w usłudze w celu uzyskania dokumentacji.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Udokumentowanie wystawionych certyfikatów przez wszystkie urzędy certyfikacji (CA)

Wyczerpujący zestaw wszystkich certyfikatów wystawionych w ciągu ostatnich 12 miesięcy powinien zostać udostępniony w dokumentacji.

> [!IMPORTANT]
> Przykładowa aplikacja magazynu OPC obsługuje pobieranie wszystkich certyfikatów używanych i wygenerowanych w usłudze w celu uzyskania dokumentacji.

### <a name="document-the-sop-for-securely-deleting-cryptographic-keys"></a>Udokumentowanie SOP w celu bezpiecznego usunięcia kluczy kryptograficznych

Usunięcie klucza może wystąpić tylko sporadycznie w okresie istnienia urzędu certyfikacji, dlatego nie ma żadnego użytkownika nie ma przypisanego certyfikatu magazynu kluczy i dlaczego interfejsy API nie są uwidocznione, aby usunąć certyfikat wystawcy urzędu certyfikacji. Ręczna standardowa procedura operacyjna służąca do bezpiecznego usuwania kluczy kryptograficznych urzędu certyfikacji jest dostępna tylko przez bezpośrednie uzyskiwanie dostępu do magazyn kluczy w Azure Portal i przez usunięcie grupy certyfikatów w magazynie kluczy. Aby zapewnić natychmiastowe usunięcie, należy wyłączyć [usuwanie nietrwałe magazynu](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) kluczy.

## <a name="certificates"></a>Certyfikaty

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Certyfikaty muszą być zgodne z minimalnym profilem certyfikatu

Usługa magazynu OPC to urzędy certyfikacji online, które wystawiają certyfikaty jednostek końcowych dla subskrybentów.
Mikrousługa magazynu OPC jest zgodna z poniższymi wskazówkami w implementacji domyślnej.

- Wszystkie certyfikaty muszą zawierać następujące pola X. 509 podane poniżej:
  - Zawartość pola wersji musi być v3. 
  - Zawartość pola numer seryjny musi zawierać co najmniej 8 bajtów entropii uzyskanych z FIPS (Federal Information Processing Standards) 140, zatwierdzony generator liczb losowych.<br>
    > [!IMPORTANT]
    > Numer seryjny magazynu OPC jest domyślnie 20 bajtów i uzyskiwany z generatora liczb losowych kryptograficznych systemu operacyjnego. Generator liczb losowych to FIPS 140 zatwierdzony na urządzeniach z systemem Windows, ale nie w wersjach systemu Linux. Ten fakt należy wziąć pod uwagę w przypadku wybrania wdrożenia usługi, które korzysta z maszyn wirtualnych z systemem Linux lub kontenerów platformy Docker systemu Linux, na których podstawowa technologia OpenSSL nie jest zgodna ze standardem FIPS 140.
  - Pola issuerUniqueID i subjectUniqueID nie mogą być obecne.
  - Certyfikaty jednostki końcowej muszą być zidentyfikowane przy użyciu rozszerzenia podstawowych warunków ograniczających zgodnie z IETF RFC 5280.
  - Pole pathLenConstraint musi mieć wartość 0 dla certyfikatu wystawiającego urzędu certyfikacji. 
  - Rozszerzenie rozszerzonego użycia klucza musi być obecne i zawierać minimalny zestaw rozszerzonych identyfikatorów obiektów użycia klucza (OID). Nie można określić identyfikatora OID anyExtendedKeyUsage (2.5.29.37.0). 
  - Rozszerzenie punktu dystrybucji listy CRL musi być obecne w certyfikacie urzędu certyfikacji wystawcy.<br>
    > [!IMPORTANT]
    > Rozszerzenie punktu dystrybucji list CRL (CDP) znajduje się w certyfikatach urzędu certyfikacji magazynu OPC, niemniej jednak OPC UA używają metod niestandardowych do dystrybucji list CRL.
  - Rozszerzenie dostępu do informacji o urzędach musi być obecne w certyfikatach subskrybenta.<br>
    > [!IMPORTANT]
    > Rozszerzenie dostępu do informacji o urzędach jest obecne w certyfikatach subskrybenta magazynu OPC, ale jednak OPC UA używają metod niestandardowych do dystrybucji informacji o urzędzie certyfikacji wystawcy.
- Należy używać zatwierdzonych algorytmów asymetrycznych, długości kluczy, funkcji skrótu i trybów uzupełniania.
  - Są to jedyne obsługiwane algorytmy **RSA** i **SHA-2** (*).
  - Klucz RSA może służyć do szyfrowania, wymiany kluczy i podpisywania.
  - Szyfrowanie RSA musi używać tylko trybów uzupełniania OAEP, RSA-KEM lub RSA-PSS.
  - Długość klucza > = 2048 bitów jest wymagana.
  - Użyj rodziny SHA-2 algorytmów wyznaczania wartości skrótu (SHA256, SHA384 i SHA512).
  - Klucze głównego urzędu certyfikacji RSA o typowym okresie istnienia > = 20 lat musi wynosić 4096 bitów lub więcej.
  - Klucze urzędu certyfikacji wystawcy RSA muszą mieć co najmniej 2048 bitów; Jeśli data wygaśnięcia certyfikatu urzędu certyfikacji przypada po 2030, klucz urzędu certyfikacji musi mieć wartość 4096 bitów lub większą.
- Okres istnienia certyfikatu
  - Certyfikaty głównego urzędu certyfikacji: Maksymalny okres ważności certyfikatu dla głównych urzędów certyfikacji nie może przekroczyć 25 lat.
  - Podrzędny urząd certyfikacji lub certyfikaty wystawcy urzędu certyfikacji w trybie online: Maksymalny okres ważności certyfikatu dla urzędów certyfikacji, które są w trybie online i tylko problemy z certyfikatami subskrybenta nie może przekroczyć sześciu lat. Dla tych urzędów certyfikacji pokrewny klucz podpisu prywatnego nie może być używany dłużej niż trzy lata do wystawiania nowych certyfikatów.<br>
    > [!IMPORTANT]
    > Certyfikat wystawcy generowany w ramach domyślnej mikrousługi magazynu OPC bez zewnętrznego głównego urzędu certyfikacji jest traktowany jak podrzędny urząd certyfikacji online z odpowiednimi wymaganiami i okresami istnienia. Domyślny okres istnienia jest ustawiany na pięć lat o długości klucza > = 2048.
  - Wszystkie klucze asymetryczne muszą mieć maksymalny okres pięciu lat, zalecany okres istnienia jednego roku.<br>
    > [!IMPORTANT]
    > Domyślnie okresy istnienia certyfikatów aplikacji wystawionych za pomocą magazynu OPC mają okres istnienia wynoszący 2 lata i powinny być zastępowane co roku. 
  - Za każdym razem, gdy certyfikat zostanie odnowiony, jest odnawiany przy użyciu nowego klucza.
- OPC rozszerzenia specyficzne dla UA w certyfikatach wystąpień aplikacji
  - Rozszerzenie subjectAltName zawiera identyfikator URI i nazwy hostów aplikacji, które mogą zawierać również adresy FQDN, IPv4 i IPv6.
  - Użycie tego przykładu obejmuje bity digitalSignature, inkeyEncipherment i dataEncipherment.
  - ExtendedKeyUsage zawiera obiektu ServerAuth i/lub clientAuth.
  - AuthorityKeyIdentifier jest określony w podpisanych certyfikatach.

### <a name="certificate-authority-ca-keys-and-certificates-must-meet-minimum-requirements"></a>Klucze urzędu certyfikacji i certyfikaty muszą spełniać minimalne wymagania

- **Klucze prywatne**: Klucze **RSA** muszą mieć co najmniej 2048 bitów; Jeśli data wygaśnięcia certyfikatu urzędu certyfikacji przypada po 2030, klucz urzędu certyfikacji musi mieć wartość 4096 bitów lub większą.
- **Okres istnienia**: Maksymalny okres ważności certyfikatu dla urzędów certyfikacji, które są w trybie online i tylko problemy z certyfikatami subskrybenta nie może przekroczyć sześciu lat. Dla tych urzędów certyfikacji pokrewny klucz podpisu prywatnego nie może być używany dłużej niż trzy lata do wystawiania nowych certyfikatów.

### <a name="ca-keys-are-protected-using-hardware-security-modules-hsm"></a>Klucze urzędu certyfikacji są chronione za pomocą sprzętowych modułów zabezpieczeń (HSM)

- OpcVault używa wersji Premium magazynu kluczy platformy Azure, a klucze są chronione przez moduły zabezpieczeń (HSM) standardu FIPS 140-2 Level 2. 

Moduły kryptograficzne, które Key Vault używają, czy moduł HSM lub oprogramowanie są sprawdzone (Federal Information Processing Standards).<br>
Klucze utworzone lub zaimportowane jako chronione przez moduł HSM są przetwarzane w module HSM, zweryfikowane pod kątem standardu FIPS 140-2 Level 2.<br>
Klucze utworzone lub zaimportowane jako chronione przez oprogramowanie są przetwarzane w ramach modułów kryptograficznych zweryfikowanych pod kątem standardu FIPS 140-2 Level 1.

## <a name="operational-practices"></a>Praktyki operacyjne

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Udokumentowanie i Obsługa standardowych praktyk infrastruktury PKI na potrzeby rejestracji certyfikatów

Udokumentowanie i Obsługa standardowych procedur operacyjnych (SPD) dotyczących sposobu wystawiania certyfikatów przez urzędy certyfikacji, w tym: 
- Jak jest identyfikowany i uwierzytelniany subskrybent 
- Jak żądanie certyfikatu jest przetwarzane i sprawdzane (jeśli ma to zastosowanie), należy również uwzględnić sposób przetwarzania żądań odnowienia i ponownego generowania certyfikatu. 
- Jak wystawione certyfikaty są dystrybuowane do subskrybentów 

OPC SOP magazynu usługi Magazyn jest opisany w przeglądzie [](overview-opc-vault-architecture.md) i sposobach [zarządzania](howto-opc-vault-manage.md) dokumentami. Praktyki są zgodne ze specyfikacją OPC Unified Architecture 12: Odnajdywanie i usługi globalne.


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Udokumentowanie i Obsługa standardowych praktyk infrastruktury PKI na potrzeby odwoływania certyfikatów

Proces odwoływania certyfikatu został opisany w temacie [Omówienie](overview-opc-vault-architecture.md) i [Zarządzanie](howto-opc-vault-manage.md) dokumentami.
    
### <a name="document-certification-authority-key-generation-ceremony"></a>Dokument generowanie klucza urzędu certyfikacji procedury 

Generowanie klucza urzędu certyfikacji wystawcy w mikrousłudze magazynu OPC jest uproszczone ze względu na bezpieczny magazyn w magazynie kluczy platformy Azure i opisany w temacie [jak zarządzać](howto-opc-vault-manage.md) dokumentacją.

Jednak gdy jest używany zewnętrzny główny urząd certyfikacji, generacja klucza urzędu certyfikacji (CA) procedury musi spełniać następujące wymagania:

Procedury generowania klucza urzędu certyfikacji należy wykonać w odniesieniu do opisanego skryptu, który zawiera co najmniej następujące elementy: 
1. Definicja ról i obowiązków uczestników
2. Zatwierdzenie do przeprowadzenia generowania klucza urzędu certyfikacji procedury
3. Sprzęt kryptograficzny i materiały aktywacji wymagane przez procedury
4. Przygotowanie sprzętu (w tym aktualizowanie i wylogowywanie informacji o zasobach i konfiguracji)
5. Instalacja systemu operacyjnego
6. Określone kroki wykonywane podczas generowania klucza urzędu certyfikacji procedury, takie jak: 
7. Instalacja i konfiguracja aplikacji urzędu certyfikacji
8. Generowanie klucza urzędu certyfikacji
9. Kopia zapasowa klucza urzędu certyfikacji
10. Podpisywanie certyfikatu urzędu certyfikacji
9. Importuj klucze podpisane w chronionym module HSM usługi.
11. Zamykanie systemu urzędu certyfikacji
12. Przygotowanie materiałów do magazynowania


## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak bezpiecznie zarządzać magazynem OPC, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Bezpieczne urządzenia OPC UA z magazynem OPC](howto-opc-vault-secure.md)