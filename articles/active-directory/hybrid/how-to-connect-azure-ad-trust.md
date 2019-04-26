---
title: Usługa Azure AD Connect — zarządzania relacjami zaufania usług AD FS z usługą Azure AD za pomocą usługi Azure AD Connect | Dokumentacja firmy Microsoft
description: Informacje o operacjach zaufania usługi Azure AD obsługi przez usługę Azure AD connect.
keywords: Usługi AD FS, ADFS, zarządzanie usług AD FS, AAD Connect, Połącz, usługi Azure AD, zaufanie, usługi AAD, oświadczenia, oświadczenia, roszczenie reguł, wystawiania, przekształcania, reguł, tworzenia kopii zapasowych, przywracania
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bd46bb820c7127c4fa6105fcc0be73bb66024c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245709"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Zarządzania relacjami zaufania usług AD FS z usługą Azure AD za pomocą usługi Azure AD Connect

## <a name="overview"></a>Omówienie

Program Azure AD Connect można zarządzać federacji między lokalną Active Directory Federation Services (AD FS) i Azure AD. Ten artykuł zawiera omówienie:

* Różne ustawienia skonfigurowane na zaufania przy użyciu usługi Azure AD Connect
* Ustaw reguły przekształcania wystawiania (reguły oświadczeń), przy użyciu usługi Azure AD Connect
* Sposób tworzenia kopii zapasowych i przywracania roszczenia reguł między uaktualnienia i konfiguracji aktualizacji. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Ustawienia kontrolowane przez program Azure AD Connect

Program Azure AD Connect zarządza **tylko** ustawienia związane z zaufania usługi Azure AD. Program Azure AD Connect nie modyfikować wszystkie ustawienia na innych jednostek uzależnionych w usługach AD FS. Poniższa tabela wskazuje ustawienia, które są kontrolowane przez program Azure AD Connect.

| Ustawienie | Opis |
| :--- | :--- |
| Certyfikat podpisywania tokenu | Program Azure AD Connect może służyć do resetowania i ponowne utworzenie relacji zaufania z usługą Azure AD. Program Azure AD Connect jest jednorazowe, natychmiastowe przerzucania certyfikaty podpisywania tokenu usług AD FS i aktualizuje ustawienia Federacji domeny usługi Azure AD.|
| Algorytm podpisywania tokenu | Firma Microsoft zaleca używanie algorytmu SHA-256 jako algorytm podpisywania tokenu. Program Azure AD Connect może wykryć, jeśli algorytm podpisywania tokenu jest ustawiony na wartość mniej bezpieczna niż algorytm SHA-256. W następnej operacji możliwych konfiguracji powoduje zaktualizowanie ustawień algorytmu SHA-256. Inne zaufania jednostki uzależnionej musi zostać zaktualizowany do użycia nowy certyfikat podpisywania tokenu. |
| Identyfikator relacji zaufania usługi Azure AD | Program Azure AD Connect, ustawia wartość prawidłowy identyfikator zaufania usługi Azure AD. Usługi AD FS jednoznacznie identyfikuje zaufania usługi Azure AD za pomocą wartości identyfikatora. |
| Punkty końcowe usługi AD systemu Azure | Program Azure AD Connect zapewnia, że zawsze są punkty końcowe skonfigurowane dla zaufania usługi Azure AD zgodnie z najnowszych zalecane wartości, odporność i wydajność. |
| Reguły przekształcania wystawiania | Istnieją liczby reguł oświadczeń, które są potrzebne w celu uzyskania optymalnej wydajności funkcji usługi Azure AD w środowisku federacyjnym. Program Azure AD Connect zapewnia się, że zawsze Konfiguracja zaufania usługi Azure AD przy użyciu odpowiedniego zestawu reguł zalecanych oświadczeń. |
| Alternate-id | Jeśli synchronizacja jest skonfigurowany do używania alternatywny identyfikator, program Azure AD Connect umożliwia skonfigurowanie usług AD FS do uwierzytelniania przy użyciu alternatywnego identyfikatora. |
| Metadane automatycznej aktualizacji | Zaufania z usługą Azure AD jest skonfigurowana dla aktualizacji automatycznych metadanych. Usługi AD FS okresowo sprawdza dostępność metadanych relacji zaufania usługi Azure AD i wciąż aktualne w przypadku, gdy zmienia się po stronie usługi Azure AD. |
| Zintegrowane uwierzytelnianie systemu Windows | Podczas operacji tworzenia sprzężenia hybrydowej usługi Azure AD IWA jest włączone dla rejestracji urządzeń, które ułatwiają dołączanie do hybrydowej usługi Azure AD dla urządzeń z niskiego poziomu |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Wykonanie przepływów i ustawienia Federacji skonfigurowane przy użyciu usługi Azure AD Connect

Usługa Azure AD connect nie aktualizuje wszystkie ustawienia dla zaufania usługi Azure AD podczas konfiguracji przepływów. Zmodyfikowane ustawień zależą od tego, które przepływ zadań lub wykonania jest wykonywana. W poniższej tabeli wymieniono ustawienia wpływ w ramach wykonywania różnych przepływów.

| Przepływ wykonania | Wpływ ustawienia |
| :--- | :--- |
| Pierwsza instalacja — dostęp próbny (express) | Brak |
| Najpierw przejść instalacji (nowej farmy usług AD FS) | Utworzono nową farmę usług AD FS i relację zaufania z usługą Azure AD jest tworzona od podstaw. |
| Najpierw przejść instalacji (istniejącej farmy usług AD FS, istniejącą relację zaufania usługi Azure AD) | Identyfikator relacji zaufania usługi Azure AD, reguły przekształcania wystawiania, punktów końcowych usługi Azure AD, alternatywny identyfikator (w razie potrzeby), aktualizacji automatycznych metadanych |
| Resetuj relację zaufania usługi Azure AD | Token podpisywania certyfikatu podpisywania algorytmu, identyfikator relacji zaufania usługi Azure AD, przekształcania wystawiania tokenu reguł, punktów końcowych usługi Azure AD, alternatywny identyfikator (w razie potrzeby), aktualizacji automatycznych metadanych |
| Dodawanie serwera federacyjnego | Brak |
| Dodaj serwer proxy aplikacji sieci Web | Brak |
| Opcje urządzenia | Reguły przekształcania wystawiania, IWA rejestracji urządzeń |
| Dodawanie domeny federacyjnej | Jeśli domena jest dodawany po raz pierwszy, oznacza to, instalacji uległ zmianie z jednej domeny federacyjnej do wielu domen federacyjnych — program Azure AD Connect będzie ponownie utworzyć zaufanie od podstaw. Jeśli już skonfigurowano relacji zaufania z usługą Azure AD dla wielu domen, są modyfikowane tylko reguły przekształcania wystawiania |
| Aktualizacja protokołu SSL | Brak |

Podczas wszystkich operacji, w którym, wszystkie ustawienia jest sprawia, że AD Connect modyfikacji, Azure kopię zapasową bieżących ustawień zaufania w **%ProgramData%\AADConnect\ADFS**

![Usługa Azure AD Connect strony przedstawiający komunikat dotyczący istniejącej kopii zapasowej zaufania usługi Azure AD](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Przed wersją 1.1.873.0 kopii zapasowej obejmowało tylko reguły przekształcania wystawiania, a ich kopie zapasowe w pliku dziennika śledzenia kreatora.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Reguły przekształcania wystawiania ustawione przez program Azure AD Connect

Program Azure AD Connect zapewnia się, że zawsze Konfiguracja zaufania usługi Azure AD przy użyciu odpowiedniego zestawu reguł zalecanych oświadczeń. Firma Microsoft zaleca używanie programu Azure AD connect do zarządzania relacji zaufania usługi Azure AD. Ta sekcja zawiera zestaw reguł przekształcania wystawiania i ich opisy.

| Nazwa reguły | Opis |
| --- | --- |
| Problem nazwy UPN | Ta reguła bada wartość userprincipalname od atrybutu skonfigurowanego w ustawieniach synchronizacji userPrincipalName.|
| Zapytania objectguid i msdsconsistencyguid niestandardowe oświadczenia ImmutableId | Ta reguła dodaje wartości tymczasowej w potoku w celu objectguid i msdsconsistencyguid wartość, jeśli istnieje |
| Do sprawdzania istnienia msdsconsistencyguid | Oparte na tego, czy wartość msdsconsistencyguid istnieje lub nie, firma Microsoft tymczasowej, aby ustawić flagę rozwiązania do zastosowania jako wartość ImmutableId bezpośrednie |
| Wystawiać msdsconsistencyguid jako Niemodyfikowalny identyfikator, jeśli istnieje | Wystawiać msdsconsistencyguid jako wartość ImmutableId, jeśli wartość istnieje |
| Wystawiać objectGuidRule, jeśli reguła msdsConsistencyGuid nie istnieje. | Jeśli wartość msdsconsistencyguid nie istnieje, wartość objectguid zostaną wystawione jako wartość ImmutableId |
| Nameidentifier problemem | Ta zasada generuje wartość oświadczenia nameidentifier.|
| Accounttype problem dla komputerów przyłączonych do domeny | Jeśli jednostka uwierzytelniane urządzenia połączonego z domeną, ta zasada wystawiania typ konta jako DJ oznaczającą urządzenia połączonego z domeną |
| Problem z wartości użytkownika dla konta, gdy nie jest kontem komputera | Jeśli jednostki uwierzytelniane jest użytkownik, ta zasada generuje typ konta jako użytkownik |
| Wydawanie issuerid, gdy nie jest kontem komputera | Ta zasada generuje wartość issuerId podczas uwierzytelniania jednostki nie jest to urządzenie. Wartość jest tworzony za pomocą wyrażenia regularnego, który jest skonfigurowany przy użyciu usługi Azure AD Connect. Wyrażenie regularne jest tworzony, po uwzględnieniu wszystkich domen federacyjnych, za pomocą usługi Azure AD Connect. |
| Issuerid problem dla uwierzytelniania komputera DJ | Ta zasada generuje wartość issuerId, gdy jednostka uwierzytelniania jest urządzeniem |
| Onpremobjectguid problem w przypadku komputerów przyłączonych do domeny | Jednostki uwierzytelniane w przypadku urządzenia połączonego z domeną, ta zasada generuje objectguid lokalnych dla urządzenia |
| Przekazuj podstawowy identyfikator SID | Ta zasada generuje podstawowy identyfikator SID uwierzytelniania jednostki |
| Przekazuj oświadczenie - insideCorporateNetwork | Problemy dotyczące tej reguły oświadczenia, który pomaga w usłudze Azure AD, czy uwierzytelnianie pochodzi z wewnątrz sieci firmowej lub na zewnątrz |
| Przekazuj oświadczenie — Psso |   |
| Wystawiania oświadczeń wygaśnięcia hasła | Problemy dotyczące tej reguły trzech oświadczeń czas wygaśnięcia hasła, liczba dni dla hasło wygaśnie jednostki uwierzytelniane i adres URL gdzie kierować do zmiany hasła.|
| Przekazuj oświadczenie — authnmethodsreferences | Wartość oświadczenia wydane w ramach tej reguły wskazuje, jakiego typu uwierzytelniania została wykonana dla jednostki |
| Przekazuj oświadczenie - multifactorauthenticationinstant | Wartość tego oświadczenia określa czas, w formacie UTC, gdy uwierzytelnianie wieloskładnikowe wielu ostatnio wykonanych przez użytkownika. |
| Przekazuj oświadczenie - AlternateLoginID | Ta reguła wystawia oświadczenia AlternateLoginID w przypadku uwierzytelniania za pomocą identyfikatora logowania alternatywnej. |

> [!NOTE]
> Reguł oświadczeń dla nazwy UPN problemu i ImmutableId będą się różnić, jeśli używasz innego niż domyślny wybór podczas konfiguracji programu Azure AD Connect

## <a name="restore-issuance-transform-rules"></a>Przywróć reguły przekształcania wystawiania

Wersja usługi Azure AD Connect 1.1.873.0 lub nowszej sprawia, że kopii zapasowej usługi Azure AD ufa ustawienia po każdej aktualizacji ustawień zaufania usługi Azure AD. Ustawienia zaufania usługi Azure AD kopie zapasowe są tworzone w **%ProgramData%\AADConnect\ADFS**. Nazwa pliku jest w następującym formacie AadTrust -&lt;data&gt;-&lt;czasu&gt;.txt, na przykład - AadTrust-20180710-150216.txt

![Zrzut ekranu przedstawiający przykładowy tworzenie kopii zapasowych zaufania usługi Azure AD](./media/how-to-connect-azure-ad-trust/backup.png)

Można przywrócić reguły przekształcania wystawiania, wykonując poniższe kroki sugerowane

1. Otwórz interfejs użytkownika zarządzania usług AD FS w Menedżerze serwera
2. Otwórz właściwości relacji zaufania usługi Azure AD, przechodząc **usług AD FS &gt; jednostki uzależnionej relacja zaufania jednostek uzależnionych &gt; platforma tożsamości usługi Microsoft Office 365 &gt; Edytuj zasady wystawiania oświadczeń**
3. Kliknij pozycję **Dodaj regułę**
4. W oświadczenie szablonu reguły, wybierz pozycję wysyłanie oświadczeń przy użyciu reguły niestandardowej, a następnie kliknij pozycję **dalej**
5. Skopiuj nazwę reguły oświadczeń z pliku kopii zapasowej i wklej go w polu **Nazwa reguły oświadczeń**
6. Kopiuj regułę oświadczenia z pliku kopii zapasowej w polu tekstowym dla **reguły niestandardowej** i kliknij przycisk **Zakończ**

> [!NOTE]
> Upewnij się, że dodatkowe reguły nie powodują konfliktów z regułami skonfigurowane przy użyciu usługi Azure AD Connect.

## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie i dostosowywanie usług federacyjnych Active Directory za pomocą usługi Azure AD Connect](how-to-connect-fed-management.md)
