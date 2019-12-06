---
title: Zarządzanie użytkownikami
description: Opisuje sposób zarządzania użytkownikami
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 37dacf0adfc9e3dcbab963cef50e2ee5209df314
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852356"
---
# <a name="manage-users"></a>Zarządzanie użytkownikami

Usługa Azure FarmBeats obejmuje zarządzanie użytkownikami dla osób, które są częścią Azure Active Directory (Azure AD). Możesz dodać użytkowników do wystąpienia usługi Azure FarmBeats, aby uzyskać dostęp do interfejsów API, wyświetlić wygenerowane mapy i dane telemetryczne czujnika z farmy.

## <a name="prerequisites"></a>Wymagania wstępne

- Wymagane jest wdrożenie usługi Azure FarmBeats. Zobacz [wdrażanie FarmBeats](prepare-for-deployment.md) , aby dowiedzieć się więcej na temat konfigurowania usługi Azure FarmBeats.
- Identyfikator poczty e-mail użytkowników, które chcesz dodać lub usunąć z wystąpienia usługi Azure FarmBeats.

## <a name="manage-azure-farmbeats-users"></a>Zarządzanie użytkownikami usługi Azure FarmBeats

Usługa Azure FarmBeats używa usługi Azure AD do uwierzytelniania, kontroli dostępu i ról. Użytkowników w dzierżawie usługi Azure AD można dodać jako użytkowników w usłudze Azure FarmBeats.

> [!NOTE]
> Jeśli użytkownik, którego próbujesz dodać, nie istnieje w dzierżawie usługi Azure AD, postępuj zgodnie z instrukcjami w sekcji **Dodawanie użytkowników usługi Azure AD** , aby zakończyć instalację, przed przystąpieniem do konfigurowania ich jako użytkowników usługi Azure FarmBeats.

**Role**

Obecnie istnieją dwa rodzaje ról użytkowników, które są obsługiwane w usłudze Azure FarmBeats:

 - **Administrator** — dostęp do interfejsów API usługi Azure FarmBeats Data Hub. Użytkownicy w tej roli mogą wysyłać zapytania dotyczące wszystkich obiektów usługi Azure FarmBeats Data Hub, wykonywać wszystkie operacje z akceleratora FarmBeats.
 - **Tylko do odczytu** — dostęp tylko do odczytu do interfejsów API FarmBeats Data Hub. Użytkownicy mogą wyświetlać interfejsy API centrum danych, pulpity nawigacyjne akceleratora i mapy. Użytkownik z rolą "tylko do odczytu" nie będzie mógł wykonywać żadnych operacji, takich jak generowanie map, kojarzenie urządzeń lub tworzenie Farm.


## <a name="add-user-to-azure-farmbeats"></a>Dodawanie użytkownika do usługi Azure FarmBeats

Aby dodać użytkownika do usługi Azure FarmBeats, 
1.  Zaloguj się do akceleratora i kliknij ikonę ustawień
2.  Kliknij Access Control

    ![Farmy projektów](./media/create-farms/settings-users-1.png)

3.  Wprowadź identyfikator poczty e-mail użytkownika, do którego chcesz udzielić dostępu
4.  Wybierz żądaną rolę — Administrator lub tylko do odczytu
5.  Kliknij pozycję Dodaj rolę

Dodani użytkownicy będą teraz mogli uzyskiwać dostęp do usługi Azure FarmBeats (zarówno centrum danych, jak i Akcelerator).

## <a name="delete-user-from-azure-farmbeats"></a>Usuwanie użytkownika z usługi Azure FarmBeats

Aby usunąć użytkownika z systemu Azure FarmBeats, możesz
1.  Zaloguj się do akceleratora i kliknij ikonę ustawień
2.  Kliknij Access Control
3.  Kliknij ikonę usuwania obok identyfikatora e-mail użytkownika, który chcesz usunąć.

Użytkownik zostanie usunięty z systemu. Zostanie wyświetlony następujący komunikat z potwierdzeniem pomyślnej operacji


![Farmy projektów](./media/create-farms/manage-users-2.png)


## <a name="add-azure-ad-users"></a>Dodawanie użytkowników usługi Azure AD

> [!NOTE]
> Wykonaj następujące kroki, tylko jeśli użytkownik próbujący uzyskać dostęp do usługi Azure FarmBeats nie znajduje się w dzierżawie usługi Azure AD. Jeśli użytkownik już istnieje, nie trzeba wykonywać poniższych kroków

Aby można było przypisać je do aplikacji i ról, użytkownicy FarmBeats muszą istnieć w dzierżawie usługi Azure AD. Aby dodać użytkowników do usługi Azure AD, wykonaj następujące czynności:
1.  Zaloguj się do [portalu Azure](https://portal.azure.com/).
2.  Wybierz swoje konto w prawym górnym rogu i przejdź do dzierżawy usługi Azure AD skojarzonej z FarmBeats
3.  Wybierz **Azure Active Directory > użytkowników**. Zostanie wyświetlona lista użytkowników w katalogu.
4.  Aby dodać użytkowników do katalogu, wybierz pozycję **nowy użytkownik**. W przypadku użytkowników zewnętrznych wybierz pozycję **nowy użytkownik-Gość**.

    ![Farmy projektów](./media/create-farms/manage-users-3.png)

5.  Wypełnij pola wymagane dla nowego użytkownika. Wybierz pozycję **Utwórz**.

Więcej informacji na temat zarządzania użytkownikami w usłudze Azure AD można znaleźć w dokumentacji usługi [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/) .

## <a name="next-steps"></a>Następne kroki

Wdrożono usługę Azure FarmBeats. Teraz Dowiedz się, jak [tworzyć farmy](manage-farms.md#create-farms).

