---
title: Zarządzanie użytkownikami i rolami w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Jako administrator, jak zarządzać użytkownikami i rolami w aplikacji IoT Central platformy Azure
author: lmasieri
ms.author: lmasieri
ms.date: 10/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 79bb039a1950c9ff56c77e74f244aba7bfe8dd05
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839371"
---
# <a name="manage-users-and-roles-in-your-iot-central-application-preview-features"></a>Zarządzanie użytkownikami i rolami w aplikacji IoT Central (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

W tym artykule opisano, jak administrator może dodawać, edytować i usuwać użytkowników w aplikacji IoT Central platformy Azure. W tym artykule opisano również sposób zarządzania rolami w aplikacji IoT Central platformy Azure.

Aby uzyskać dostęp do sekcji **Administracja** i korzystać z niej, musisz mieć rolę **administratora** dla aplikacji IoT Central platformy Azure. Jeśli utworzysz aplikację IoT Central platformy Azure, zostanie ona automatycznie dodana do roli **administratora** dla tej aplikacji.

## <a name="add-users"></a>Dodawanie użytkowników

Każdy użytkownik musi mieć konto użytkownika, aby można było zalogować się i uzyskać dostęp do aplikacji IoT Central platformy Azure. Konta Microsoft (kont MSA) i Azure Active Directory (Azure AD) są obsługiwane w usłudze Azure IoT Central. Grupy Azure Active Directory nie są obecnie obsługiwane w usłudze Azure IoT Central.

Aby uzyskać więcej informacji, zobacz [konto Microsoft pomocy](https://support.microsoft.com/products/microsoft-account?category=manage-account) i [szybkiego startu: Dodawanie nowych użytkowników do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Aby dodać użytkownika do aplikacji IoT Central, przejdź do strony **Użytkownicy** w sekcji **Administracja** .
    
    > [!div class="mx-imgBorder"]
    >![Zarządzanie użytkownikami](media/howto-manage-users-roles-pnp/manage-users-pnp.png)

1. Aby dodać użytkownika, na stronie **Użytkownicy** wybierz pozycję **+ Dodaj użytkownika**.

1. Wybierz rolę dla użytkownika z menu rozwijanego **rola** . Dowiedz się więcej o rolach w sekcji [Zarządzanie rolami](#manage-roles) w tym artykule.

    > [!div class="mx-imgBorder"]
    >![Dodaj użytkownika i wybierz rolę](media/howto-manage-users-roles-pnp/add-user-pnp.png)

    > [!NOTE]
    > Użytkownik, który znajduje się w roli niestandardowej, która przyzna im uprawnienia do dodawania innych użytkowników, może dodawać tylko użytkowników do roli z tymi samymi lub mniej uprawnień niż ich własna rola.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Edytuj role przypisane do użytkowników

Ról nie można zmienić po ich przypisaniu. Aby zmienić rolę przypisaną do użytkownika, Usuń użytkownika, a następnie ponownie Dodaj użytkownika z inną rolą.

> [!NOTE]
> Przypisane role są specyficzne dla aplikacji IoT Central i nie mogą być zarządzane w witrynie Azure Portal.

## <a name="delete-users"></a>Usuwanie użytkowników

Aby usunąć użytkowników, zaznacz jedno lub więcej pól wyboru na stronie **Użytkownicy** . Następnie wybierz pozycję **Usuń**.

## <a name="manage-roles"></a>Zarządzanie rolami

Role umożliwiają kontrolowanie, kto w organizacji może wykonywać różne zadania w IoT Central. Istnieją trzy wbudowane role, które można przypisać do użytkowników aplikacji. Możesz również [utworzyć role niestandardowe](#create-a-custom-role) , jeśli potrzebujesz precyzyjnej kontroli.

> [!div class="mx-imgBorder"]
> ![zarządzać wyborem ról](media/howto-manage-users-roles-pnp/manage-roles-pnp.png)

### <a name="administrator"></a>Administrator

Użytkownicy z rolą **administrator** mogą zarządzać każdą częścią aplikacji i kontrolować ją, w tym rozliczenia.

Użytkownik, który tworzy aplikację, jest automatycznie przypisywany do roli **administratora** . W roli **administratora** zawsze musi być co najmniej jeden użytkownik.

### <a name="builder"></a>Konstruktor

Użytkownicy w roli **konstruktora** mogą zarządzać każdą częścią aplikacji, ale nie mogą wprowadzać zmian na kartach Administracja lub ciągła eksport danych.

### <a name="operator"></a>Operator

Użytkownicy w roli **operatora** mogą monitorować kondycję i stan urządzenia. Nie mogą wprowadzać zmian w szablonach urządzeń ani administrować aplikacjami. Operatory mogą dodawać i usuwać urządzenia, zarządzać zestawami urządzeń oraz uruchamiać analizy i zadania. 

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Jeśli rozwiązanie wymaga dokładniejszych kontroli dostępu, można utworzyć niestandardowe role z niestandardowymi zestawami uprawnień. Aby utworzyć rolę niestandardową, przejdź do strony **role** w sekcji **Administracja** aplikacji. Następnie wybierz pozycję **+ Nowa rola**, a następnie Dodaj nazwę i opis roli. Wybierz uprawnienia wymagane przez rolę, a następnie wybierz pozycję **Zapisz**.

Użytkowników można dodawać do roli niestandardowej w taki sam sposób, jak w przypadku dodawania użytkowników do roli wbudowanej.

> [!div class="mx-imgBorder"]
> ![utworzyć niestandardową rolę](media/howto-manage-users-roles-pnp/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opcje roli niestandardowej

Podczas definiowania roli niestandardowej należy wybrać zestaw uprawnień, które użytkownik otrzymuje, jeśli są członkami roli. Niektóre uprawnienia są zależne od innych. Jeśli na przykład dodasz uprawnienie **Aktualizowanie pulpitów nawigacyjnych aplikacji** do roli, zostanie automatycznie dodane uprawnienie **Wyświetl pulpity nawigacyjne aplikacji** . Poniższa tabela zawiera podsumowanie dostępnych uprawnień i ich zależności, których można użyć podczas tworzenia ról niestandardowych.

#### <a name="managing-devices"></a>Zarządzanie urządzeniami

**Uprawnienia szablonu urządzenia**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Zarządzaj | Widok <br/> Inne zależności: wyświetlanie wystąpień urządzeń  |
| Pełna kontrola | Wyświetlanie, zarządzanie <br/> Inne zależności: wyświetlanie wystąpień urządzeń |

**Uprawnienia wystąpienia urządzenia**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: wyświetlanie szablonów urządzeń i grup urządzeń |
| Aktualizacja | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń i grup urządzeń  |
| Tworzenie | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń i grup urządzeń  |
| Usuwanie | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń i grup urządzeń  |
| Wykonaj polecenia | Aktualizuj, Wyświetl <br/> Inne zależności: wyświetlanie szablonów urządzeń i grup urządzeń  |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie, usuwanie, wykonywanie poleceń <br/> Inne zależności: wyświetlanie szablonów urządzeń i grup urządzeń  |

**Uprawnienia grup urządzeń**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: wyświetlanie szablonów urządzeń i wystąpień urządzeń |
| Aktualizacja | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń i wystąpień urządzeń   |
| Tworzenie | Wyświetl, Aktualizuj <br/> Inne zależności: wyświetlanie szablonów urządzeń i wystąpień urządzeń   |
| Usuwanie | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń i wystąpień urządzeń   |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie i usuwanie <br/> Inne zależności: wyświetlanie szablonów urządzeń i wystąpień urządzeń |

**Uprawnienia do zarządzania łącznością urządzeń**

| Nazwa | Zależności |
| ---- | -------- |
| Odczytaj wystąpienie | Brak <br/> Inne zależności: wyświetlanie szablonów urządzeń, grup urządzeń, wystąpień urządzeń |
| Zarządzanie instnace | Brak |
| Odczytaj Global | Brak   |
| Zarządzaj globalnym | Odczytaj Global |
| Pełna kontrola | Odczyt wystąpienia, Zarządzanie wystąpieniem, odczyt globalny, zarządzanie globalne. <br/> Inne zależności: wyświetlanie szablonów urządzeń, grup urządzeń, wystąpień urządzeń |

**Uprawnienia zadań**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń |
| Aktualizacja | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń |
| Tworzenie | Wyświetl, Aktualizuj <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń |
| Usuwanie | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń |
| Realizacja | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń; Aktualizowanie wystąpień urządzenia; Wykonywanie poleceń w wystąpieniach urządzeń |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie, usuwanie, wykonywanie <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń; Aktualizowanie wystąpień urządzenia; Wykonywanie poleceń w wystąpieniach urządzeń |

**Uprawnienia reguł**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: wyświetlanie szablonów urządzeń |
| Aktualizacja | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń |
| Tworzenie | Wyświetl, Aktualizuj <br/> Inne zależności: wyświetlanie szablonów urządzeń |
| Usuwanie | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie i usuwanie <br/> Inne zależności: wyświetlanie szablonów urządzeń |

#### <a name="managing-the-app"></a>Zarządzanie aplikacją

**Uprawnienia do ustawień aplikacji**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Copy | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń, grup urządzeń, pulpitów nawigacyjnych, eksportowanie danych, znakowanie, linki pomocy, role niestandardowe i reguły |
| Usuwanie | Widok   |
| Pełna kontrola | Wyświetlanie, aktualizowanie, kopiowanie, usuwanie <br/> Inne zależności: wyświetlanie szablonów urządzeń, grup urządzeń, pulpitów nawigacyjnych aplikacji, eksportowanie danych, znakowanie, linki pomocy, role niestandardowe i reguły |

**Uprawnienia eksportowania szablonu aplikacji**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Eksportowanie | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń, grup urządzeń, pulpitów nawigacyjnych, eksportowanie danych, znakowanie, linki pomocy, role niestandardowe i reguły |
| Pełna kontrola | Wyświetl, Eksportuj <br/> Inne zależności: wyświetlanie szablonów urządzeń, grup urządzeń, pulpitów nawigacyjnych aplikacji, eksportowanie danych, znakowanie, linki pomocy, role niestandardowe i reguły |

**Uprawnienia do rozliczeń**

| Nazwa | Zależności |
| ---- | -------- |
| Zarządzaj | Brak     |
| Pełna kontrola | Zarządzaj |

#### <a name="managing-users-and-roles"></a>Zarządzanie użytkownikami i rolami

**Uprawnienia ról niestandardowych**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak |
| Aktualizacja | Widok |
| Tworzenie | Wyświetl, Aktualizuj |
| Usuwanie | Widok |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie i usuwanie |

**Uprawnienia do zarządzania użytkownikami**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: Wyświetlanie ról niestandardowych |
| Add | Widok <br/> Inne zależności: Wyświetlanie ról niestandardowych |
| Usuwanie | Widok <br/> Inne zależności: Wyświetlanie ról niestandardowych |
| Pełna kontrola | Wyświetlanie, Dodawanie, usuwanie <br/> Inne zależności: Wyświetlanie ról niestandardowych |

> [!NOTE]
> Użytkownik, który znajduje się w roli niestandardowej, która przyzna im uprawnienia do dodawania innych użytkowników, może dodawać tylko użytkowników do roli z tymi samymi lub mniej uprawnień niż ich własna rola.

#### <a name="customizing-the-app"></a>Dostosowywanie aplikacji

**Uprawnienia pulpitu nawigacyjnego aplikacji**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Tworzenie | Wyświetl, Aktualizuj |
| Usuwanie | Widok   |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie i usuwanie |

**Osobiste uprawnienia pulpitów nawigacyjnych**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Tworzenie | Wyświetl, Aktualizuj   |
| Usuwanie | Widok   |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie i usuwanie |

**Uprawnienia do znakowania, favicon i kolorów**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Pełna kontrola | Wyświetl, Aktualizuj |

**Uprawnienia do linków**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Pełna kontrola | Wyświetl, Aktualizuj |

#### <a name="extending-the-app"></a>Rozszerzanie aplikacji

**Uprawnienia eksportu danych**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Tworzenie | Wyświetl, Aktualizuj  |
| Usuwanie | Widok   |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie i usuwanie |

**Uprawnienia tokenu interfejsu API**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Tworzenie | Widok   |
| Usuwanie | Widok   |
| Pełna kontrola | Wyświetlanie, tworzenie, usuwanie |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać użytkownikami i rolami w aplikacji IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z informacjami na temat [zarządzania rachunkiem](howto-view-bill-pnp.md).
