---
title: 'Samouczek:  Konfigurowanie wersji jako kopii zapasowej dla usług AD FS w Azure AD Connect | Dokumentacja firmy Microsoft'
description: Pokazuje, jak włączenie synchronizacji skrótów haseł do przechowywania kopii zapasowych i dla usług AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 01/30/2019
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 7e7c9ca30659fd1e99989bb77406b653a8ed9e7f
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55303102"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Samouczek:  Konfigurowanie wersji jako kopii zapasowej dla usług AD FS w Azure AD Connect

Następującego samouczka przeprowadzi Konfigurowanie synchronizacji skrótów haseł jako kopii zapasowej i pracy awaryjnej dla usług AD FS.  W tym dokumencie również pokażemy, jak włączenie synchronizacji skrótów haseł jako metody uwierzytelniania podstawowego usług AD FS nie powiodło się lub staną się niedostępne.

## <a name="prerequisites"></a>Wymagania wstępne
Ten samouczek opiera się na [samouczka: Sfederuj jednego środowiska lasu usługi AD w chmurze](tutorial-federation.md) i jest na wymagany przed podjęciem próby wykonania tego samouczka.  Jeśli nie zostało zakończone w tym samouczku, zrób to przed podjęciem próby wykonania procedury w tym dokumencie.

## <a name="enable-phs-in-azure-ad-connect"></a>Włączanie wersji w usłudze Azure AD Connect
Pierwszym krokiem, skoro mamy już środowisko Azure AD Connect, które jest stosowania mechanizmu Federacji, jest włączanie synchronizacji skrótów haseł i umożliwić usłudze Azure AD Connect do synchronizowania skrótów.

Wykonaj następujące czynności:

1.  Kliknij dwukrotnie ikonę program Azure AD Connect, który został utworzony na pulpicie
2.  Kliknij pozycję **Konfiguruj**.
3.  Na stronie dodatkowych zadań, wybrać **Dostosowywanie opcji synchronizacji** i kliknij przycisk **dalej**.
4.  Wprowadź nazwę użytkownika i hasło administratora globalnego.  To konto zostało utworzone [tutaj](tutorial-federation.md#create-a-global-administrator-in-azure-ad) w poprzednim samouczku.
5.  Na **Podłączanie katalogów** ekranu, kliknij przycisk **dalej**.
6.  Na **domenę i jednostkę Organizacyjną filtrowanie** ekranu, kliknij przycisk **dalej**.
7.  Na **funkcje opcjonalne** ekranu wyboru **synchronizacji skrótów haseł** i kliknij przycisk **dalej**.
![Wybierz](media/tutorial-phs-backup/backup1.png)</br>
8.  Na **wszystko gotowe do skonfigurowania** ekranu kliknij **Konfiguruj**.
9.  Po zakończeniu konfiguracji kliknij **zakończenia**.
10. Gotowe.  Wszystko będzie gotowe.  Synchronizacja skrótów haseł zostanie teraz przeprowadzona i może służyć do przechowywania kopii zapasowych, jeśli usługi AD FS, staje się niedostępny.

## <a name="switch-to-password-hash-synchronization"></a>Przełącz się do synchronizacji skrótów haseł
Teraz pokażemy jak przełączyć się do synchronizacji skrótów haseł, jeśli potrzebujesz.  Wykonaj następujące czynności:

1. Kliknij dwukrotnie ikonę program Azure AD Connect, który został utworzony na pulpicie
2.  Kliknij pozycję **Konfiguruj**.
3.  Wybierz **zmiana użytkownika logowania** i kliknij przycisk **dalej**.
![Change](media/tutorial-phs-backup/backup2.png)</br>
4.  Wprowadź nazwę użytkownika i hasło administratora globalnego.  To konto zostało utworzone [tutaj](tutorial-federation.md#create-a-global-administrator-in-azure-ad) w poprzednim samouczku.
5.  Na **logowania użytkownika** ekranu, wybierz opcję **synchronizacji skrótów haseł** i należy zaznaczyć w **nie Konwertuj kont użytkowników** pole.  
6.  Pozostaw wartość domyślną **włączyć rejestrację jednokrotną** zaznaczone, a następnie kliknij przycisk **dalej**.
7.  Na **włączyć rejestrację jednokrotną** ekranu kliknij **dalej**.
8.  Na **wszystko gotowe do skonfigurowania** ekranu, kliknij przycisk **Konfiguruj**.
9.  Po zakończeniu konfiguracji kliknij **zakończenia**.
10. Użytkownicy mogą teraz używać swoich haseł do logowania się na platformie Azure i Azure services.

## <a name="test-signing-in-with-one-of-our-users"></a>Testowanie logujesz się przy użyciu jednego z naszych użytkowników

1.  Przejdź do [http://myapps.microsoft.com](http://myapps.microsoft.com)
2. Zaloguj się przy użyciu konta użytkownika, który został utworzony w naszym nowym dzierżawcą.  Konieczne będzie zalogowanie się przy użyciu następującego formatu: (user@domain.onmicrosoft.com). Użyj tego samego hasła, których użytkownik używa do logowania lokalnego.</br>
![Sprawdź](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="next-steps"></a>Następne kroki


- [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) 
- [Ustawienia ekspresowe](how-to-connect-install-express.md)
- [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md)|
