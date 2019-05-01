---
title: 'Samouczek:  Konfigurowanie synchronizacji skrótów haseł jako zapasu dla usług AD FS w programie Azure AD Connect | Microsoft Docs'
description: Opisuje sposób włączania synchronizacji skrótów haseł jako zapasu dla usług AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5ad7badfa44a006fd7e71d3b0e42ee95ac698d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919011"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Samouczek:  Konfigurowanie synchronizacji skrótów haseł jako zapasu dla usług AD FS w programie Azure AD Connect

Następujący samouczek przeprowadzi Cię przez konfigurowanie synchronizacji skrótów haseł jako zapasu i trybu failover dla usług AD FS.  W tym dokumencie przedstawimy także sposób włączenia synchronizacji skrótów haseł jako podstawowej metody uwierzytelniania w przypadku awarii lub niedostępności usług AD FS.

>[!NOTE] 
>Mimo że te kroki są zazwyczaj wykonywane podczas awaryjnego lub w sytuacjach awarii, zaleca się te kroki testu, a następnie sprawdź własnych procedur, zanim wystąpi awaria.

>[!NOTE]
>W przypadku, gdy nie masz dostępu do serwera Azure AD Connect lub serwer nie ma dostępu do Internetu, możesz skontaktować się ze [Microsoft Support](https://support.microsoft.com/en-us/contactus/) uzyskanymi zmiany po stronie usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne
Ten samouczek opiera się na artykule [Samouczek: Federowanie środowiska pojedynczego lasu usługi AD z chmurą](tutorial-federation.md), który jest wymaganiem wstępnym dla przejścia tego samouczka.  Jeśli tamten samouczek nie został wykonany, zrób to przed podjęciem próby wykonania kroków zawartych w tym dokumencie.

>[!IMPORTANT]
>Przed przełączeniem do wersji, należy utworzyć kopię zapasową środowiska usług AD FS.  Można to zrobić za pomocą [usługi AD FS szybkiego przywrócenia narzędzie](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

## <a name="enable-phs-in-azure-ad-connect"></a>Włączanie synchronizacji skrótów haseł w programie Azure AD Connect
Pierwszym krokiem w środowisku programu Azure AD Connect korzystającym z federacji jest włączenie synchronizacji skrótów haseł i umożliwienie programowi Azure AD Connect synchronizowania skrótów.

Wykonaj następujące czynności:

1.  Kliknij dwukrotnie ikonę programu Azure AD Connect utworzoną na pulpicie.
2.  Kliknij pozycję **Konfiguruj**.
3.  Na stronie Zadania dodatkowe wybierz pozycję **Dostosuj opcje synchronizacji**, a następnie kliknij pozycję **Dalej**.
4.  Podaj nazwę użytkownika i hasło administratora globalnego.  To konto zostało utworzone [tutaj](tutorial-federation.md#create-a-global-administrator-in-azure-ad) w poprzednim samouczku.
5.  Na ekranie **Łączenie katalogów** kliknij pozycję **Dalej**.
6.  Na ekranie **Filtrowanie domen i jednostek organizacyjnych** kliknij przycisk **Dalej**.
7.  Na ekranie **Funkcje opcjonalne** zaznacz opcję **Synchronizacja skrótów haseł** i kliknij pozycję **Dalej**.
![Wybierz](media/tutorial-phs-backup/backup1.png)</br>
8.  Na ekranie **Wszystko gotowe do skonfigurowania** kliknij pozycję **Konfiguruj**.
9.  Po zakończeniu konfigurowania kliknij pozycję **Zakończ**.
10. Gotowe.  Wszystko jest zrobione.  Synchronizacja skrótów haseł zostanie teraz przeprowadzona i będzie można jej używać jako zapasu, jeśli usługi AD FS staną się niedostępne.

## <a name="switch-to-password-hash-synchronization"></a>Przejście na synchronizację skrótów haseł
Teraz pokażemy, jak przejść na synchronizację skrótów haseł. Przed rozpoczęciem zastanów się nad warunkami wykonania przejścia. Nie rób tego z przyczyn tymczasowych, takich jak awaria sieci, pomniejszy problem z usługami AD FS lub problem wpływający na część użytkowników. Jeśli podejmiesz decyzję o wykonaniu przejścia, ponieważ rozwiązanie problemu trwałoby zbyt długo, wykonaj następujące kroki:

> [!IMPORTANT]
> Należy pamiętać, że jej zajmie trochę czasu hasła skróty do synchronizacji z usługą Azure AD.  Oznacza to, że może zająć nawet 3 godzin na potrzeby synchronizacji zakończyć i przed rozpoczęciem uwierzytelniania za pomocą skrótów haseł.

1. Kliknij dwukrotnie ikonę programu Azure AD Connect utworzoną na pulpicie.
2.  Kliknij pozycję **Konfiguruj**.
3.  Wybierz pozycję **Zmień dane logowania użytkownika** i kliknij pozycję **Dalej**.
![Zmień](media/tutorial-phs-backup/backup2.png)</br>
4.  Podaj nazwę użytkownika i hasło administratora globalnego.  To konto zostało utworzone [tutaj](tutorial-federation.md#create-a-global-administrator-in-azure-ad) w poprzednim samouczku.
5.  Na ekranie **Logowanie użytkownika** wybierz pozycję **Synchronizacja skrótów haseł** i zaznacz pole **Nie konwertuj kont użytkowników**.  
6.  Pozostaw domyślne zaznaczenie pola **Włącz logowanie jednokrotne**, a następnie kliknij pozycję **Dalej**.
7.  Na ekranie **Włącz logowanie jednokrotne** kliknij pozycję **Dalej**.
8.  Na ekranie **Wszystko gotowe do skonfigurowania** kliknij pozycję **Konfiguruj**.
9.  Po zakończeniu konfigurowania kliknij pozycję **Zakończ**.
10. Użytkownicy mogą teraz używać swoich haseł do logowania się do platformy Azure i usług platformy Azure.

## <a name="test-signing-in-with-one-of-our-users"></a>Testowanie logowania się przy użyciu jednego z kont użytkowników

1. Przejdź na stronę [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Zaloguj się przy użyciu konta użytkownika utworzonego w nowej dzierżawie.  Należy zalogować się przy użyciu następującego formatu: (user@domain.onmicrosoft.com). Użyj tego samego hasła, za pomocą którego użytkownik loguje się lokalnie.</br>
   ![Weryfikacja](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Przejdź z powrotem do Federacji
Teraz pokażemy sposób wrócić do Federacji.  Aby to zrobić, wykonaj następujące czynności:

1.  Kliknij dwukrotnie ikonę programu Azure AD Connect utworzoną na pulpicie.
2.  Kliknij pozycję **Konfiguruj**.
3.  Wybierz pozycję **Zmień dane logowania użytkownika** i kliknij pozycję **Dalej**.
4.  Podaj nazwę użytkownika i hasło administratora globalnego.  To konto, które zostało utworzone [tutaj](tutorial-federation.md#create-a-global-administrator-in-azure-ad) w poprzednim samouczku.
5.  Na **logowania użytkownika** ekranu, wybierz opcję **Federacja z usługami AD FS** i kliknij przycisk **dalej**.  
6. Na stronie Poświadczenia administratora domeny wprowadź nazwę użytkownika oraz hasło konta contoso\Administrator, a następnie kliknij przycisk **Dalej**.
7. Na ekranie farmy usług AD FS, kliknij **dalej**.
8. Na **domeny usługi Azure AD** ekranu, wybierz domenę z listy rozwijanej i kliknij przycisk **dalej**.
9. Na ekranie **Wszystko gotowe do skonfigurowania** kliknij pozycję **Konfiguruj**.
10. Po zakończeniu konfiguracji kliknij **dalej**.
![Konfigurowanie](media/tutorial-phs-backup/backup4.png)</br>
11. Na **Zweryfikuj łączność Federacji** ekranu, kliknij przycisk **Sprawdź**.  Może być konieczne skonfigurowanie rekordów DNS (Dodaj rekordy A i AAAA), aby zakończyć się pomyślnie.
![Weryfikacja](media/tutorial-phs-backup/backup5.png)</br>
12. Kliknij przycisk **zakończenia**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Resetowanie relacji zaufania systemu Azure i usług AD FS
Teraz należy zresetować relację zaufania między usług AD FS i Azure.

1.  Kliknij dwukrotnie ikonę programu Azure AD Connect utworzoną na pulpicie.
2.  Kliknij pozycję **Konfiguruj**.
3.  Wybierz **Zarządzanie Federacji** i kliknij przycisk **dalej**.
4.  Wybierz **zaufania Resetowanie usługi Azure AD** i kliknij przycisk **dalej**.
![Resetuj](media/tutorial-phs-backup/backup6.png)</br>
5.  Na **nawiązywanie połączenia z usługi Azure AD** ekranu, wprowadź nazwę użytkownika i hasło dla administratora globalnego.
6.  Na **nawiązywanie połączenia z usługami AD FS** ekranu, wprowadź nazwę użytkownika contoso\Administrator i hasło i kliknij przycisk **dalej.**
7.  Na **certyfikaty** ekranu, kliknij przycisk **dalej**.

## <a name="test-signing-in-with-one-of-our-users"></a>Testowanie logowania się przy użyciu jednego z kont użytkowników

1.  Przejdź na stronę [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Zaloguj się przy użyciu konta użytkownika utworzonego w nowej dzierżawie.  Należy zalogować się przy użyciu następującego formatu: (user@domain.onmicrosoft.com). Użyj tego samego hasła, za pomocą którego użytkownik loguje się lokalnie.
![Weryfikacja](media/tutorial-password-hash-sync/verify1.png)

W ten sposób pomyślnie skonfigurowano środowisko tożsamości hybrydowej, które można wykorzystać do testowania lub do bliższego zapoznania się z możliwościami platformy Azure.

## <a name="next-steps"></a>Kolejne kroki


- [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) 
- [Ustawienia ekspresowe](how-to-connect-install-express.md)
- [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md)
