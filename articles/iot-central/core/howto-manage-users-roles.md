---
title: Zarządzanie użytkownikami i rolami w aplikacji Azure IoT Central | Dokumenty firmy Microsoft
description: Jak zarządzać użytkownikami i rolami w aplikacji Azure IoT Central jako administrator
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: c00f9d8baa55ef0d0cf6322ee71f22e739e6acdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365513"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Zarządzanie użytkownikami i rolami w aplikacji IoT Central

W tym artykule opisano, jak jako administrator możesz dodawać, edytować i usuwać użytkowników w aplikacji Usługi Azure IoT Central. W tym artykule opisano również sposób zarządzania rolami w aplikacji Azure IoT Central.

Aby uzyskać dostęp do sekcji **Administracja** i korzystać z niej, musisz być w roli **administratora** dla aplikacji Azure IoT Central. Jeśli tworzysz aplikację Azure IoT Central, zostaniesz automatycznie dodany do roli **administratora** dla tej aplikacji.

## <a name="add-users"></a>Dodawanie użytkowników

Każdy użytkownik musi mieć konto użytkownika, zanim będzie mógł zalogować się i uzyskać dostęp do aplikacji Azure IoT Central. Konta Microsoft i konta usługi Azure Active Directory są obsługiwane w usłudze Azure IoT Central. Grupy usługi Azure Active Directory nie są obecnie obsługiwane w usłudze Azure IoT Central.

Aby uzyskać więcej informacji, zobacz [Pomoc dotycząca konta Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) i szybki [start: Dodawanie nowych użytkowników do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Aby dodać użytkownika do aplikacji IoT Central, przejdź do strony **Użytkownicy** w sekcji **Administracja.**
    
    > [!div class="mx-imgBorder"]
    >![Zarządzanie użytkownikami](media/howto-manage-users-roles/manage-users-pnp.png)

1. Aby dodać użytkownika, na stronie **Użytkownicy** wybierz pozycję **+ Dodaj użytkownika**.

1. Wybierz rolę dla użytkownika z menu rozwijanego **Rola.** Dowiedz się więcej o rolach w sekcji [Zarządzanie rolami](#manage-roles) w tym artykule.

    > [!div class="mx-imgBorder"]
    >![Dodawanie użytkownika i wybieranie roli](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Użytkownik, który jest w roli niestandardowej, która udziela im uprawnienia do dodawania innych użytkowników, można tylko dodać użytkowników do roli z taką samą lub mniejszą liczbę uprawnień niż ich własnej roli.

Jeśli identyfikator użytkownika usługi IoT Central zostanie usunięty z usługi Azure Active Directory, a następnie odczytany, użytkownik nie będzie mógł zalogować się w aplikacji IoT Central. Aby ponownie włączyć dostęp, administrator centrum IoT powinien usunąć i odczytać użytkownika w aplikacji.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Edytowanie ról przypisanych do użytkowników

Ról nie można zmieniać po ich przypisaniu. Aby zmienić rolę przypisaną do użytkownika, usuń użytkownika, a następnie dodaj go ponownie z inną rolą.

> [!NOTE]
> Przypisane role są specyficzne dla aplikacji IoT Central i nie można nimi zarządzać za pomocą witryny Azure Portal.

## <a name="delete-users"></a>Usuwanie użytkowników

Aby usunąć użytkowników, zaznacz jedno lub więcej pól wyboru na stronie **Użytkownicy.** Następnie wybierz pozycję **Usuń**.

## <a name="manage-roles"></a>Zarządzanie rolami

Role umożliwiają kontrolowanie, kto w organizacji może wykonywać różne zadania w centrum IoT. Istnieją trzy wbudowane role, które można przypisać do użytkowników aplikacji. Można również [utworzyć role niestandardowe,](#create-a-custom-role) jeśli wymagana jest kontrola z drobniejszym ziarnistym.

> [!div class="mx-imgBorder"]
> ![Zarządzanie zaznaczaniem ról](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Administrator

Użytkownicy w roli **Administrator** mogą zarządzać i kontrolować każdą część aplikacji, w tym rozliczenia.

Użytkownik, który tworzy aplikację jest automatycznie przypisywany do roli **Administrator.** W roli **Administrator** zawsze musi znajdować się co najmniej jeden użytkownik.

### <a name="builder"></a>Konstruktor

Użytkownicy w roli **Konstruktora** mogą zarządzać każdą częścią aplikacji, ale nie mogą wprowadzać zmian na kartach Administracja lub Ciągły eksport danych.

### <a name="operator"></a>Operator

Użytkownicy w roli **Operator** mogą monitorować kondycję i stan urządzenia. Nie mogą wprowadzać zmian w szablonach urządzeń ani administrować aplikacją. Operatorzy mogą dodawać i usuwać urządzenia, zarządzać zestawami urządzeń oraz uruchamiać analizy i zadania. 

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Jeśli rozwiązanie wymaga bardziej ziarnistych kontroli dostępu, można utworzyć role niestandardowe z niestandardowymi zestawami uprawnień. Aby utworzyć rolę niestandardową, przejdź do strony **Role** w sekcji **Administracja** aplikacji. Następnie wybierz **+ Nowa rola**i dodaj nazwę i opis swojej roli. Wybierz uprawnienia wymagane przez twoją rolę, a następnie wybierz pozycję **Zapisz**.

Użytkownicy można dodawać do roli niestandardowej w taki sam sposób, jak dodajesz użytkowników do wbudowanej roli.

> [!div class="mx-imgBorder"]
> ![Tworzenie roli niestandardowej](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Niestandardowe opcje roli

Podczas definiowania roli niestandardowej, należy wybrać zestaw uprawnień, które użytkownik jest przyznawany, jeśli są członkami roli. Niektóre uprawnienia są zależne od innych. Na przykład po dodaniu uprawnienia **Aktualizuj pulpity nawigacyjne aplikacji** do roli, **uprawnienie Wyświetl pulpity nawigacyjne aplikacji** zostanie automatycznie dodane. W poniższych tabelach podsumowano dostępne uprawnienia i ich zależności, których można użyć podczas tworzenia ról niestandardowych.

#### <a name="managing-devices"></a>Zarządzanie urządzeniami

**Uprawnienia szablonu urządzenia**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Zarządzaj | Widok <br/> Inne zależności: wyświetlanie wystąpień urządzenia  |
| Pełna kontrola | Wyświetlanie, zarządzanie <br/> Inne zależności: wyświetlanie wystąpień urządzenia |

**Uprawnienia wystąpienia urządzenia**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: wyświetlanie szablonów urządzeń i grup urządzeń |
| Aktualizacja | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń i grup urządzeń  |
| Utwórz | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń i grup urządzeń  |
| Usuń | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń i grup urządzeń  |
| Wykonywanie poleceń | Aktualizacja, Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń i grup urządzeń  |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie, usuwanie, wykonywanie poleceń <br/> Inne zależności: wyświetlanie szablonów urządzeń i grup urządzeń  |

**Uprawnienia do grup urządzeń**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: wyświetlanie szablonów urządzeń i wystąpień urządzeń |
| Aktualizacja | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń i wystąpień urządzeń   |
| Utwórz | Widok, Aktualizacja <br/> Inne zależności: wyświetlanie szablonów urządzeń i wystąpień urządzeń   |
| Usuń | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń i wystąpień urządzeń   |
| Pełna kontrola | Widok, aktualizacja, tworzenie, usuwanie <br/> Inne zależności: wyświetlanie szablonów urządzeń i wystąpień urządzeń |

**Uprawnienia do zarządzania łącznością urządzenia**

| Nazwa | Zależności |
| ---- | -------- |
| Odczyt wystąpienia | Brak <br/> Inne zależności: wyświetlanie szablonów urządzeń, grup urządzeń, wystąpień urządzeń |
| Zarządzanie wystąpieniem | Brak |
| Czytaj globalnie | Brak   |
| Zarządzanie globalnymi | Czytaj globalnie |
| Pełna kontrola | Odczyt, Zarządzaj wystąpieniem, Czytaj globalnie, Zarządzaj globalnym. <br/> Inne zależności: wyświetlanie szablonów urządzeń, grup urządzeń, wystąpień urządzeń |

**Uprawnienia zadań**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń |
| Aktualizacja | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń |
| Utwórz | Widok, Aktualizacja <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń |
| Usuń | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń |
| Realizacja | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń; Aktualizowanie wystąpień urządzeń; Wykonywanie poleceń w wystąpieniach urządzeń |
| Pełna kontrola | Widok, aktualizacja, tworzenie, usuwanie, wykonywanie <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń; Aktualizowanie wystąpień urządzeń; Wykonywanie poleceń w wystąpieniach urządzeń |

**Uprawnienia reguł**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: wyświetlanie szablonów urządzeń |
| Aktualizacja | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń |
| Utwórz | Widok, Aktualizacja <br/> Inne zależności: wyświetlanie szablonów urządzeń |
| Usuń | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń |
| Pełna kontrola | Widok, aktualizacja, tworzenie, usuwanie <br/> Inne zależności: wyświetlanie szablonów urządzeń |

#### <a name="managing-the-app"></a>Zarządzanie aplikacją

**Uprawnienia ustawień aplikacji**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Copy | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń, grup urządzeń, pulpitów nawigacyjnych, eksportu danych, znakowania, linków pomocy, ról niestandardowych, reguł |
| Usuń | Widok   |
| Pełna kontrola | Widok, aktualizacja, kopiowanie, usuwanie <br/> Inne zależności: wyświetlanie szablonów urządzeń, grup urządzeń, pulpitów nawigacyjnych aplikacji, eksportu danych, znakowania, linków pomocy, ról niestandardowych, reguł |

**Uprawnienia do eksportu szablonu aplikacji**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Eksportowanie | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń, grup urządzeń, pulpitów nawigacyjnych, eksportu danych, znakowania, linków pomocy, ról niestandardowych, reguł |
| Pełna kontrola | Widok, Eksport <br/> Inne zależności: wyświetlanie szablonów urządzeń, grup urządzeń, pulpitów nawigacyjnych aplikacji, eksportu danych, znakowania, linków pomocy, ról niestandardowych, reguł |

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
| Utwórz | Widok, Aktualizacja |
| Usuń | Widok |
| Pełna kontrola | Widok, aktualizacja, tworzenie, usuwanie |

**Uprawnienia do zarządzania użytkownikami**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: wyświetlanie ról niestandardowych |
| Dodaj | Widok <br/> Inne zależności: wyświetlanie ról niestandardowych |
| Usuń | Widok <br/> Inne zależności: wyświetlanie ról niestandardowych |
| Pełna kontrola | Widok, Dodawanie, Usuwanie <br/> Inne zależności: wyświetlanie ról niestandardowych |

> [!NOTE]
> Użytkownik, który jest w roli niestandardowej, która udziela im uprawnienia do dodawania innych użytkowników, można tylko dodać użytkowników do roli z taką samą lub mniejszą liczbę uprawnień niż ich własnej roli.

#### <a name="customizing-the-app"></a>Dostosowywanie aplikacji

**Uprawnienia pulpitu nawigacyjnego aplikacji**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Utwórz | Widok, Aktualizacja |
| Usuń | Widok   |
| Pełna kontrola | Widok, aktualizacja, tworzenie, usuwanie |

**Uprawnienia do pulpitów nawigacyjnych osobistych**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Utwórz | Widok, Aktualizacja   |
| Usuń | Widok   |
| Pełna kontrola | Widok, aktualizacja, tworzenie, usuwanie |

**Uprawnienia do znakowania, faviconu i kolorów**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Pełna kontrola | Widok, Aktualizacja |

**Uprawnienia do łączy Pomocy**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Pełna kontrola | Widok, Aktualizacja |

#### <a name="extending-the-app"></a>Rozszerzanie aplikacji

**Uprawnienia do eksportu danych**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Utwórz | Widok, Aktualizacja  |
| Usuń | Widok   |
| Pełna kontrola | Widok, aktualizacja, tworzenie, usuwanie |

**Uprawnienia tokenu interfejsu API**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Utwórz | Widok   |
| Usuń | Widok   |
| Pełna kontrola | Widok, tworzenie, usuwanie |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiesz się, jak zarządzać użytkownikami i rolami w aplikacji Azure IoT Central, następnym krokiem jest dowiedzenie się, jak [zarządzać rachunkiem.](howto-view-bill.md)
