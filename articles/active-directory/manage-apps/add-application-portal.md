---
title: Szybki start — dodawanie aplikacji do dzierżawy usługi Azure Active Directory
description: Ten przewodnik Szybki start używa witryny Azure Portal do dodawania aplikacji z galerii do dzierżawy usługi Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 650a4f643f170fc64dd0e0643d645a7dcbffdfbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240492"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Szybki start: dodawanie aplikacji do dzierżawy usługi Azure Active Directory

Usługa Azure Active Directory (Azure AD) udostępnia galerię, która zawiera tysiące wstępnie zintegrowanych aplikacji. Niektóre z aplikacji używanych w Twojej organizacji prawdopodobnie znajdują się w galerii. Ten przewodnik Szybki start używa witryny Azure Portal do dodawania aplikacji z galerii do dzierżawy usługi Azure Active Directory (Azure AD).

Po dodaniu aplikacji do dzierżawy usługi Azure AD, można:

- Zarządzanie dostępem użytkowników do aplikacji za pomocą zasad dostępu warunkowego.
- Konfigurować konta użytkowników do logowania jednokrotnego do aplikacji za pomocą kont usługi Azure AD.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby dodać aplikację do swojej dzierżawy, potrzebujesz:

- Subskrypcji usługi Azure AD
- Subskrypcji z włączonym logowaniem jednokrotnym dla aplikacji

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator globalny dzierżawy usługi Azure AD, administrator aplikacji w chmurze lub administrator aplikacji.

Aby przetestować kroki opisane w tym samouczku, zalecamy użycie środowiska innego niż produkcyjne. Jeśli nie masz nieprodukcyjnego środowiska usługi Azure AD, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Dodawanie aplikacji do dzierżawy usługi Azure AD

Aby dodać aplikację z galerii do dzierżawy usługi Azure AD:

1. W [witrynie Azure portal](https://portal.azure.com)po lewej stronie panelu nawigacji wybierz pozycję **Azure Active Directory**.

2. W okienku **Usługi Active Directory wybierz** pozycję **Aplikacje przedsiębiorstwa**. Zostanie otwarte okienko **Wszystkie aplikacje** i zostanie wyświetlone losowe próbki aplikacji w dzierżawie usługi Azure AD.

3. Aby dodać aplikację galerii do dzierżawy, wybierz pozycję **Nowa aplikacja**. 

    ![Wybierz nową aplikację, aby dodać aplikację galerii do dzierżawy](media/add-application-portal/new-application.png)

 4. Przełącz się do nowej galerii podglądu środowiska: W baner u góry **strony Dodaj aplikację**, wybierz link, który mówi Kliknij **tutaj, aby wypróbować nową i ulepszoną galerię aplikacji.**

5. Okienko **Przeglądaj galerię usługi Azure AD (Preview)** otwiera i wyświetla kafelki dla platform w chmurze, aplikacji lokalnych i polecanych aplikacji. Należy zauważyć, że aplikacje wymienione w **polecane aplikacje** sekcji mają ikony wskazujące, czy obsługują one federacyjne logowania jednokrotnego (Logowanie jednokrotne) i inicjowania obsługi administracyjnej.

    ![Wyszukiwanie aplikacji według nazwy lub kategorii](media/add-application-portal/browse-gallery.png)

6. Możesz przeglądać galerię aplikacji, którą chcesz dodać, lub wyszukać aplikację, wprowadzając jej nazwę w polu wyszukiwania. Następnie wybierz aplikację z wyników. W formularzu można edytować nazwę aplikacji, aby dopasować ją do potrzeb organizacji. W tym przykładzie zmieniliśmy nazwę na **GitHub-test**.

    ![Pokazuje, jak dodać aplikację z galerii](media/add-application-portal/create-application.png)

7. Wybierz **pozycję Utwórz**. Zostanie wyświetlona strona wprowadzenia z opcjami konfigurowania aplikacji w organizacji.

Gotowe dodawanie aplikacji. W kolejnych sekcjach pokazano, jak zmienić logo i edytować inne właściwości aplikacji.

## <a name="find-your-azure-ad-tenant-application"></a>Znajdowanie aplikacji dzierżawy usługi Azure AD

Załóżmy, że po przerwie wracasz do konfigurowania swojej aplikacji. Pierwszą rzeczą do zrobienia jest znalezienie aplikacji.

1. W **[witrynie Azure portal](https://portal.azure.com)** po lewej stronie panelu nawigacji wybierz pozycję **Azure Active Directory**.
1. W okienku **Usługi Active Directory wybierz** pozycję **Aplikacje przedsiębiorstwa**.
1. Z menu rozwijanego **Typ aplikacji** wybierz polecenie **Wszystkie aplikacje**, a następnie wybierz polecenie **Zastosuj**. Aby dowiedzieć się więcej na temat opcji wyświetlania, zobacz [View tenant applications (Wyświetlanie aplikacji dzierżawy)](view-applications-portal.md).
1. Zostanie wyświetlona lista wszystkich aplikacji w dzierżawie usługi Azure AD. Lista jest przykładem losowym. Aby wyświetlić więcej aplikacji, wybierz **pozycję Pokaż więcej** co najmniej jeden razy.
1. Aby szybko znaleźć aplikację w dzierżawie, wprowadź nazwę aplikacji w polu wyszukiwania i wybierz pozycję **Zastosuj**. W tym przykładzie znajduje gitHub test aplikacji dodane wcześniej.

    ![Pokazuje, jak znaleźć aplikację za pomocą pola wyszukiwania](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Konfigurowanie właściwości logowania użytkownika

Po znalezieniu aplikacji można ją otworzyć i skonfigurować właściwości aplikacji.

Aby edytować właściwości aplikacji:

1. Wybierz aplikację, aby ją otworzyć.
2. Wybierz **pozycję Właściwości,** aby otworzyć okienko właściwości do edycji.

    ![Pokazuje ekran Właściwości i edytowalne właściwości aplikacji](media/add-application-portal/edit-properties.png)

3. Poświęć chwilę na zapoznanie się z opcjami logowania. Opcje określają, w jaki sposób użytkownicy, którzy są przypisani lub nieprzypisyni do aplikacji mogą logować się do aplikacji. Opcje określają również, czy użytkownik może zobaczyć aplikację w panelu dostępu.

    - **Czy użytkownicy mogli się zalogować?** określa, czy użytkownicy przypisani do aplikacji mogą się logować.
    - **Wymagane jest przypisanie użytkownika?** określa, czy użytkownicy, którzy nie są przypisani do aplikacji, mogą się logować.
    - **Widoczne dla użytkowników?** określa, czy użytkownicy przypisani do aplikacji mogą ją zobaczyć w panelu dostępu i w programie uruchamianym O365.

4. Skorzystaj z poniższych tabel, aby wybrać najlepsze opcje dla Twoich potrzeb.

   - Zachowanie w przypadku użytkowników **przypisanych**:

       | Ustawienia właściwości aplikacji | | | Środowisko użytkowników przypisanych | |
       |---|---|---|---|---|
       | Włączono dla użytkowników w celu logowania się? | Wymagane przypisanie użytkownika? | Widoczne dla użytkowników? | Czy przypisani użytkownicy mogą się logować? | Czy przypisani użytkownicy widzą aplikację?* |
       | tak | tak | tak | tak | tak  |
       | tak | tak | nie  | tak | nie   |
       | tak | nie  | tak | tak | tak  |
       | tak | nie  | nie  | tak | nie   |
       | nie  | tak | tak | nie  | nie   |
       | nie  | tak | nie  | nie  | nie   |
       | nie  | nie  | tak | nie  | nie   |
       | nie  | nie  | nie  | nie  | nie   |

   - Zachowanie w przypadku użytkowników **nieprzypisanych**:

       | Ustawienia właściwości aplikacji | | | Środowisko użytkowników nieprzypisanych | |
       |---|---|---|---|---|
       | Masz włączono logowanie się użytkowników? | Wymagane przypisanie użytkownika? | Widoczne dla użytkowników? | Czy nieprzypisani użytkownicy mogą się logować? | Czy nieprzypisani użytkownicy widzą aplikację?* |
       | tak | tak | tak | nie  | nie   |
       | tak | tak | nie  | nie  | nie   |
       | tak | nie  | tak | tak | nie   |
       | tak | nie  | nie  | tak | nie   |
       | nie  | tak | tak | nie  | nie   |
       | nie  | tak | nie  | nie  | nie   |
       | nie  | nie  | tak | nie  | nie   |
       | nie  | nie  | nie  | nie  | nie   |

     *Czy użytkownik widzi aplikację w panelu dostępu i narzędziu do uruchamiania aplikacji usługi Office 365?

## <a name="use-a-custom-logo"></a>Używanie logo niestandardowego

Aby użyć logo niestandardowego:

1. Utwórz logo o rozmiarze 215 x 215 pikseli i zapisz je w formacie PNG.
1. Ponieważ aplikacja została już znaleziona, wybierz aplikację.
1. W lewym okienku wybierz pozycję **Właściwości**.
1. Przekaż logo.
1. Po zakończeniu wybierz pozycję **Zapisz**. 

    ![Pokazuje, jak zmienić logo na stronie Właściwości aplikacji](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > Miniatura wyświetlana w tym okienku **Właściwości** nie jest aktualizowana od razu. Można zamknąć i ponownie otworzyć właściwości, aby wyświetlić zaktualizowaną ikonę.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy aplikacja została dodana do organizacji usługi Azure AD, [wybierz metodę logowania jednokrotnego,](what-is-single-sign-on.md#choosing-a-single-sign-on-method) której chcesz użyć, i zapoznaj się z odpowiednim artykułem poniżej:

- [Konfigurowanie logowania jednokrotnego opartego na języku SAML](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurowanie logowania jednokrotnego hasła](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurowanie połączonego logowania](configure-linked-sign-on.md)
