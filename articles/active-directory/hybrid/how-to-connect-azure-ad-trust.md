---
title: Usługa Azure AD Connect — zarządzanie zaufaniem usług AD FS za pomocą usługi Azure AD Connect przy użyciu usługi Azure AD Connect | Dokumenty firmy Microsoft
description: Szczegóły operacyjne obsługi zaufania usługi Azure AD przez usługę Azure AD connect.
keywords: Usługi AD FS, usługi ADFS, zarządzanie usługami AD FS, AAD Connect, Connect, Azure AD, trust, AAD, claim, claim, claim rules, isuance, transform, rules, backup, restore
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
ms.openlocfilehash: 0f3e521fb7668305ce511aaddd63ed2cce8dfed0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331721"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Zarządzania relacjami zaufania usługi AD FS dla usługi Azure AD za pomocą usługi Azure AD Connect

## <a name="overview"></a>Omówienie

Usługa Azure AD Connect może zarządzać federacją między lokalną usługą federacji Active Directory (AD FS) a usługą Azure AD. Ten artykuł zawiera omówienie:

* Różne ustawienia skonfigurowane w zaufaniu przez usługę Azure AD Connect
* Reguły przekształcania wystaw (reguły oświadczeń) ustawione przez usługę Azure AD Connect
* Jak zrobić kopię zapasową i przywrócić reguły oświadczeń między uaktualnieniami a aktualizacjami konfiguracji. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Ustawienia kontrolowane przez usługę Azure AD Connect

Usługa Azure AD Connect zarządza **tylko** ustawieniami związanymi z zaufaniem usługi Azure AD. Usługa Azure AD Connect nie modyfikuje żadnych ustawień w innych relacjach zaufania jednostki uzależnień w usługach AD FS. Poniższa tabela wskazuje ustawienia, które są kontrolowane przez usługę Azure AD Connect.

| Ustawienie | Opis |
| :--- | :--- |
| Certyfikat podpisywania tokenów | Usługa Azure AD Connect może służyć do resetowania i ponownego tworzenia zaufania za pomocą usługi Azure AD. Usługa Azure AD Connect wykonuje jednorazowe natychmiastowe przerzucanie certyfikatów podpisywania tokenów dla usług AD FS i aktualizuje ustawienia federacji domeny usługi Azure AD.|
| Algorytm podpisywania tokenów | Firma Microsoft zaleca użycie algorytmu podpisywania tokenu sha-256. Usługa Azure AD Connect może wykryć, czy algorytm podpisywania tokenu jest ustawiony na wartość mniej bezpieczną niż SHA-256. Zaktualizuje ustawienie do SHA-256 w następnej możliwej operacji konfiguracji. Inne zaufanie jednostki uzależnione muszą zostać zaktualizowane, aby użyć nowego certyfikatu podpisywania tokenu. |
| Identyfikator zaufania usługi Azure AD | Usługa Azure AD Connect ustawia poprawną wartość identyfikatora zaufania usługi Azure AD. Usługi AD FS jednoznacznie identyfikuje zaufanie usługi Azure AD przy użyciu wartości identyfikatora. |
| Punkty końcowe usługi Azure AD | Usługa Azure AD Connect upewnia się, że punkty końcowe skonfigurowane dla zaufania usługi Azure AD są zawsze zgodne z najnowszymi zalecanymi wartościami odporności i wydajności. |
| Reguły przekształcania emisji | Istnieje liczba reguł oświadczeń, które są potrzebne do optymalnej wydajności funkcji usługi Azure AD w ustawieniu federacyjnego. Usługa Azure AD Connect zapewnia, że zaufanie usługi Azure AD jest zawsze skonfigurowane z odpowiednim zestawem zalecanych reguł oświadczeń. |
| Alternatywny identyfikator | Jeśli synchronizacja jest skonfigurowana do używania alternatywnego identyfikatora, usługa Azure AD Connect konfiguruje usługi AD FS do uwierzytelniania przy użyciu alternatywnego identyfikatora. |
| Automatyczna aktualizacja metadanych | Zaufanie z usługą Azure AD jest skonfigurowany do automatycznej aktualizacji metadanych. Usługi AD FS okresowo sprawdza metadane zaufania usługi Azure AD i aktualizują go w przypadku zmiany po stronie usługi Azure AD. |
| Zintegrowane uwierzytelnianie systemu Windows (IWA) | Podczas operacji hybrydowego dołączania usługi Azure AD IWA jest włączona do rejestracji urządzeń w celu ułatwienia hybrydowego sprzężenia usługi Azure AD dla urządzeń niższego poziomu |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Przepływy wykonywania i ustawienia federacji skonfigurowane przez usługę Azure AD Connect

Usługa Azure AD connect nie aktualizuje wszystkich ustawień zaufania usługi Azure AD podczas przepływów konfiguracji. Zmodyfikowane ustawienia zależą od tego, które zadanie lub przepływ wykonania jest wykonywany. W poniższej tabeli wymieniono ustawienia, na które mają wpływ różne przepływy wykonania.

| Przepływ wykonania | Ustawienia, na które ma wpływ |
| :--- | :--- |
| Instalacja pierwszego przejazdu (ekspresowa) | Brak |
| Instalacja pierwszego przejścia (nowa farma usług AD FS) | Tworzona jest nowa farma usług AD FS i tworzony jest zaufanie za pomocą usługi Azure AD od podstaw. |
| Instalacja pierwszego przebiegu (istniejąca farma usług AD FS, istniejące zaufanie usługi Azure AD) | Identyfikator zaufania usługi Azure AD, reguły przekształcania wystawczy, punkty końcowe usługi Azure AD, alternatywny identyfikator (w razie potrzeby), automatyczna aktualizacja metadanych |
| Resetowanie zaufania usługi Azure AD | Certyfikat podpisywania tokenu, algorytm podpisywania tokenów, identyfikator zaufania usługi Azure AD, reguły przekształcania wystawczy, punkty końcowe usługi Azure AD, alternatywny identyfikator (jeśli to konieczne), automatyczna aktualizacja metadanych |
| Dodawanie serwera federacyjnego | Brak |
| Dodawanie serwera WAP | Brak |
| Opcje urządzenia | Reguły przekształcania emisji, IWA do rejestracji urządzenia |
| Dodawanie domeny federacyjnej | Jeśli domena jest dodawana po raz pierwszy, oznacza to, że instalator zmienia się z federacji pojedynczej domeny na federację wielu domen — usługa Azure AD Connect odtworzy zaufanie od podstaw. Jeśli zaufanie z usługą Azure AD jest już skonfigurowane dla wielu domen, modyfikowane są tylko reguły przekształcania wystawczy |
| Aktualizowanie protokołu TLS | Brak |

Podczas wszystkich operacji, w których każde ustawienie jest modyfikowane, usługa Azure AD Connect tworzy kopię zapasową bieżących ustawień zaufania w **%ProgramData%\AADConnect\ADFS**

![Strona usługi Azure AD Connect z komunikatem o istniejącej kopii zapasowej zaufania usługi Azure AD](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Przed wersją 1.1.873.0 kopia zapasowa składała się tylko z reguł przekształcania wydawania i były one archiwizowane w pliku dziennika śledzenia kreatora.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Reguły przekształcania wystawy ustawione przez usługę Azure AD Connect

Usługa Azure AD Connect zapewnia, że zaufanie usługi Azure AD jest zawsze skonfigurowane z odpowiednim zestawem zalecanych reguł oświadczeń. Firma Microsoft zaleca używanie usługi Azure AD connect do zarządzania zaufaniem usługi Azure AD. W tej sekcji wymieniono zestaw reguł przekształcania wystawiania i ich opis.

| Nazwa reguły | Opis |
| --- | --- |
| Problem z numerem UPN | Ta reguła odpytywała wartość userprincipalname od atrybutu skonfigurowanego w ustawieniach synchronizacji dla userprincipalname.|
| Query objectguid i msdsconsistencyguid dla niestandardowego oświadczenia ImmutableId | Ta reguła dodaje wartość tymczasową w potoku dla objectguid i msdsconsistencyguid wartość, jeśli istnieje |
| Sprawdź istnienie msdsconsistencyguid | Na podstawie tego, czy wartość msdsconsistencyguid istnieje, ustawiamy tymczasową flagę, aby skierować to, czego użyć jako ImmutableId |
| Wystawianie msdsconsistencyguid jako niezmienny identyfikator, jeśli istnieje | Wystawianie msdsconsistencyguid jako ImmutableId, jeśli istnieje wartość |
| Rozchodzi obiektGuidRule, jeśli nie istnieje reguła msdsConsistencyGuid | Jeśli wartość msdsconsistencyguid nie istnieje, wartość objectguid zostanie wystawiona jako ImmutableId |
| Identyfikator nazwy problemu | Ta reguła wystawia wartość oświadczenia nameidentifier.|
| Typ konta problemu dla komputerów przyłączonych do domeny | Jeśli uwierzytelniona encja jest urządzeniem przyłączonym do domeny, ta reguła wystawia typ konta jako DJ oznaczający urządzenie przyłączone do domeny |
| Problem AccountType z wartością USER, gdy nie jest to konto komputera | Jeśli uwierzytelniona jednostka jest użytkownikiem, ta reguła wystawia typ konta jako użytkownik |
| Problem issuerid, gdy nie jest to konto komputera | Ta reguła wystawia wartość identyfikatora wystawcy, gdy jednostka uwierzytelniająca nie jest urządzeniem. Wartość jest tworzona za pośrednictwem wyrażenia regularnego, który jest skonfigurowany przez usługę Azure AD Connect. Wyrażenia regularne jest tworzony po uwzględnieniu wszystkich domen federacyjne przy użyciu usługi Azure AD Connect. |
| Problem issuerid dla dj komputera auth | Ta reguła wystawia wartość identyfikatora wystawcy, gdy encja uwierzytelniająca jest urządzeniem |
| Problem onpremobjectguid dla komputerów przyłączonych do domeny | Jeśli uwierzytelniona jednostka jest urządzeniem przyłączonym do domeny, ta reguła wystawia lokalny guid dla urządzenia |
| Przechodzenie przez podstawowy identyfikator SID | Ta reguła wystawia podstawowy identyfikator SID jednostki uwierzytelniającej |
| Pass through claim - insideCorporateNetwork | Ta reguła wystawia oświadczenie, które pomaga usłudze Azure AD wiedzieć, czy uwierzytelnianie pochodzi z sieci firmowej lub zewnętrznej |
| Pass Through Claim – Psso |   |
| Rozłąka z hasłami | Ta reguła wystawia trzy oświadczenia dotyczące czasu wygaśnięcia hasła, liczby dni wygaśnięcia hasła jednostki uwierzytelnionej i adresu URL, gdzie można rozlińczona w celu zmiany hasła.|
| Przejść przez roszczenia - authnmethodsreferences | Wartość w oświadczeniu wystawionym zgodnie z tą regułą wskazuje, jaki typ uwierzytelniania został wykonany dla jednostki |
| Przepuścić oświadczenie - multifactorauthenticationinstant | Wartość tego oświadczenia określa czas w czasie UTC, kiedy użytkownik ostatnio wykonywane uwierzytelnianie wieloskładnikowe. |
| Przekazywanie oświadczenia — AlternateLoginID | Ta reguła wystawia oświadczenie AlternateLoginID, jeśli uwierzytelnianie zostało wykonane przy użyciu alternatywnego identyfikatora logowania. |

> [!NOTE]
> Reguły oświadczeń dla żądania upn i immutableid różnią się, jeśli używasz wyboru domyślnego podczas konfiguracji usługi Azure AD Connect

## <a name="restore-issuance-transform-rules"></a>Przywracanie reguł przekształcania wydawania

Usługa Azure AD Connect w wersji 1.1.873.0 lub nowszej tworzy kopię zapasową ustawień zaufania usługi Azure AD za każdym razem, gdy zostanie dokonana aktualizacja ustawień zaufania usługi Azure AD. Kopia zapasowa ustawień zaufania usługi Azure AD jest archiwizowa w **%ProgramData%\AADConnect\ADFS**. Nazwa pliku jest w następującym formacie&lt;AadTrust - data&gt;-&lt;godziny&gt;.txt, na przykład - AadTrust-20180710-150216.txt

![Zrzut ekranu przedstawiający przykładową kopii zapasowej zaufania usługi Azure AD](./media/how-to-connect-azure-ad-trust/backup.png)

Można przywrócić reguły przekształcania emisji, korzystając z sugerowanych kroków poniżej

1. Otwieranie interfejsu użytkownika zarządzania usługą AD FS w Menedżerze serwera
2. Otwórz właściwości zaufania usługi Azure AD, przechodząc do zaufania **jednostki uzależnionej usługi AD &gt; FS &gt; Microsoft Office 365 Identity Platform &gt; Edit Claims Issuance Policy**
3. Kliknij regułę **Dodaj**
4. W szablonie reguły oświadczeń wybierz pozycję Wyślij oświadczenia przy użyciu reguły niestandardowej i kliknij przycisk **Dalej**
5. Skopiuj nazwę reguły oświadczenia z pliku kopii zapasowej i wklej ją w polu **Nazwa reguły oświadczeń**
6. Skopiuj regułę oświadczenia z pliku kopii zapasowej do pola tekstowego **reguły niestandardowej** i kliknij przycisk **Zakończ**

> [!NOTE]
> Upewnij się, że dodatkowe reguły nie są sprzeczne z regułami skonfigurowanym przez usługę Azure AD Connect.

## <a name="next-steps"></a>Następne kroki
* [Zarządzanie usługami federacyjnymi active directory i dostosowywanie ich przy użyciu usługi Azure AD Connect](how-to-connect-fed-management.md)
