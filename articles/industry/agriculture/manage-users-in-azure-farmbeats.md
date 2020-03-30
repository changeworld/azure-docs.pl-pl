---
title: Zarządzanie użytkownikami w usłudze Azure FarmBeats
description: W tym artykule opisano sposób zarządzania użytkownikami w usłudze Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 6ccc29422c6abf1120d13c05d10125efd0871ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502068"
---
# <a name="manage-users"></a>Zarządzanie użytkownikami

Usługa Azure FarmBeats obejmuje zarządzanie użytkownikami dla osób, które są częścią wystąpienia usługi Azure Active Directory (Azure AD). Można dodać użytkowników do wystąpienia FarmBeats platformy Azure, aby uzyskać dostęp do interfejsów API, wyświetlić wygenerowane mapy i uzyskać dostęp do danych telemetrycznych czujnika dostępu z farmy.

## <a name="prerequisites"></a>Wymagania wstępne

- Wymagana jest instalacja Usługi Azure FarmBeats. Aby uzyskać więcej informacji, zobacz [Instalowanie platformy Azure FarmBeats](install-azure-farmbeats.md).
- Identyfikatory wiadomości e-mail użytkowników, których chcesz dodać lub usunąć z wystąpienia Usługi Azure FarmBeats.

## <a name="manage-azure-farmbeats-users"></a>Zarządzanie użytkownikami usługi Azure FarmBeats

Usługa Azure FarmBeats używa usługi Azure AD do uwierzytelniania, kontroli dostępu i ról. Można dodać użytkowników w dzierżawie usługi Azure AD jako użytkowników w usłudze Azure FarmBeats.

> [!NOTE]
> Jeśli użytkownik nie jest użytkownikiem dzierżawy usługi Azure AD, postępuj zgodnie z instrukcjami w sekcji **Dodawanie użytkowników usługi Azure AD,** aby ukończyć konfigurację.

Usługa Azure FarmBeats obsługuje dwa typy ról użytkowników:

 - **Administrator:** Pełny dostęp do interfejsów API usługi Azure FarmBeats Datahub. Użytkownicy w tej roli można zbadać wszystkie obiekty Usługi Azure FarmBeats Datahub i wykonywać wszystkie operacje z FarmBeats Accelerator.
 - **Tylko do odczytu:** Dostęp tylko do odczytu do interfejsów API FarmBeats Datahub. Użytkownicy mogą wyświetlać interfejsy API usługi Datahub, pulpity nawigacyjne akceleratora i mapy. Użytkownicy z dostępem tylko do odczytu nie mogą wykonywać operacji, takich jak generowanie map, kojarzenie urządzeń lub tworzenie farm.

## <a name="add-users-to-azure-farmbeats"></a>Dodawanie użytkowników do usługi Azure FarmBeats

Aby dodać użytkowników do usługi Azure FarmBeats:

1. Zaloguj się do akceleratora, a następnie wybierz ikonę **Ustawienia.**
2. Wybierz **pozycję Kontrola dostępu**.

    ![Okienko Ustawienia farm](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Wprowadź identyfikator wiadomości e-mail użytkownika, do którego chcesz udzielić dostępu.
4. Wybierz żądaną rolę, **Administrator** lub **Tylko do odczytu**.
5. Wybierz **pozycję Dodaj rolę**.

Dodany użytkownik może teraz uzyskać dostęp do usługi Azure FarmBeats (zarówno Datahub, jak i Accelerator).

## <a name="delete-users-from-azure-farmbeats"></a>Usuwanie użytkowników z usługi Azure FarmBeats

Aby usunąć użytkowników z systemu Azure FarmBeats:

1. Zaloguj się do akceleratora, a następnie wybierz ikonę **Ustawienia.**
2. Wybierz **pozycję Kontrola dostępu**.
3. Wybierz pozycję **Usuń**.

   Użytkownik zostanie usunięty z systemu. Otrzymasz następujący komunikat potwierdzający:

   ![Komunikat potwierdzenia farmbeats platformy Azure](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Dodawanie użytkowników usługi Azure AD

> [!NOTE]
> Użytkownicy usługi Azure FarmBeats muszą istnieć w dzierżawie usługi Azure AD przed przypisaniem ich do aplikacji i ról. Jeśli użytkownik nie istnieje w dzierżawie usługi Azure AD, postępuj zgodnie z instrukcjami w tej sekcji. Pomiń instrukcje, jeśli użytkownik już istnieje w dzierżawie usługi Azure AD.

Wykonaj kroki, aby dodać użytkowników do usługi Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. W prawym górnym rogu wybierz swoje konto, a następnie przełącz się do dzierżawy usługi Azure AD skojarzonej z Kontem FarmBeats.
3. Wybierz**pozycję Użytkownicy** **usługi Azure Active Directory** > .

    Zostanie wyświetlona lista użytkowników usługi Azure AD.

4. Aby dodać użytkownika do katalogu, wybierz pozycję **Nowy użytkownik**. Aby dodać użytkownika zewnętrznego, wybierz pozycję **Nowy użytkownik-gość**.

    ![Okienko "Wszyscy użytkownicy"](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Wybierz nazwę nowego użytkownika, a następnie wypełnij wymagane pola dla tego użytkownika.
6. Wybierz **pozycję Utwórz**.

Aby uzyskać informacje dotyczące zarządzania użytkownikami usługi Azure AD, zobacz [Dodawanie lub usuwanie użytkowników w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/).

## <a name="next-steps"></a>Następne kroki

Pomyślnie dodano użytkowników do wystąpienia usługi Azure FarmBeats. Teraz dowiedz się, jak [tworzyć i zarządzać gospodarstwami.](manage-farms-in-azure-farmbeats.md#create-farms)
