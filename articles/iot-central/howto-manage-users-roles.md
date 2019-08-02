---
title: Zarządzanie użytkownikami i rolami w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Jako administrator, jak zarządzać użytkownikami i rolami w aplikacji IoT Central platformy Azure
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 937c4a4dbf976564cbf8dc562bdec3b328fc2bc0
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68729245"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Zarządzanie użytkownikami i rolami w aplikacji IoT Central

W tym artykule opisano, jak administrator może dodawać, edytować i usuwać użytkowników w aplikacji IoT Central platformy Azure oraz jak zarządzać rolami w aplikacji IoT Central platformy Azure.

Aby uzyskać dostęp do sekcji **Administracja** i korzystać z niej, musisz mieć rolę **administratora** dla aplikacji IoT Central platformy Azure. Jeśli utworzysz aplikację IoT Central platformy Azure, zostanie ona automatycznie przypisana do roli **administratora** dla tej aplikacji.


## <a name="add-users"></a>Dodawanie użytkowników

Każdy użytkownik musi mieć konto użytkownika, aby można było zalogować się i uzyskać dostęp do aplikacji IoT Central platformy Azure. Konta Microsoft (kont MSA) i Azure Active Directory (Azure AD) są obsługiwane w usłudze Azure IoT Central. Grupy Azure Active Directory nie są obecnie obsługiwane w usłudze Azure IoT Central.

Aby uzyskać więcej informacji, zobacz [konto Microsoft pomocy](https://support.microsoft.com/products/microsoft-account?category=manage-account) i [szybkiego startu: Dodawanie nowych użytkowników do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Aby dodać użytkownika do aplikacji IoT Central, przejdź do strony **Użytkownicy** w sekcji **Administracja** .

    ![Lista użytkowników](media/howto-administer/image1.png)

1. Aby dodać użytkownika, na stronie **Użytkownicy** wybierz pozycję **+ Dodaj użytkownika**.

1. Wybierz rolę dla użytkownika z menu rozwijanego **rola** . Dowiedz się więcej o rolach w sekcji [Zarządzanie rolami](#manage-roles) w tym artykule.

    ![Wybór roli](media/howto-administer/image3.png)

    > [!NOTE]
    >  Aby dodać użytkowników zbiorczo, wprowadź identyfikatory użytkowników wszystkich użytkowników, których chcesz dodać rozdzielone średnikami. Wybierz rolę z menu rozwijanego **rola** . Następnie wybierz pozycję **Zapisz**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Edytuj role przypisane do użytkowników

Ról nie można zmienić po ich przypisaniu. Aby zmienić rolę przypisaną do użytkownika, Usuń użytkownika, a następnie ponownie Dodaj użytkownika z inną rolą.

> [!NOTE]
> Przypisane role są specyficzne dla aplikacji IoT Central i nie mogą być zarządzane w witrynie Azure Portal.

## <a name="delete-users"></a>Usuwanie użytkowników

Aby usunąć użytkowników, zaznacz jedno lub więcej pól wyboru na stronie **Użytkownicy** . Następnie wybierz pozycję **Usuń**.

## <a name="manage-roles"></a>Zarządzanie rolami

Role umożliwiają kontrolę nad tym, kto w organizacji może wykonywać różne zadania w IoT Central. Istnieją trzy role, które można przypisać do użytkowników aplikacji.

### <a name="administrator"></a>Administrator

Użytkownicy z rolą **administrator** mają dostęp do wszystkich funkcji w aplikacji.

Użytkownik, który tworzy aplikację, jest automatycznie przypisywany do roli **administratora** . W roli **administratora** zawsze musi być co najmniej jeden użytkownik.

### <a name="application-builder"></a>Konstruktor aplikacji

Użytkownicy w roli programu **Application Builder** mogą wykonywać wszystkie czynności w aplikacji, z wyjątkiem administrowania aplikacją. Konstruktory mogą tworzyć, edytować i usuwać szablony i urządzenia urządzeń, zarządzać zestawami urządzeń oraz uruchamiać analizy i zadania. Konstruktorzy nie będą mieli dostępu do sekcji administrowania aplikacji.

### <a name="application-operator"></a>Operator aplikacji

Użytkownicy z rolą **operatora aplikacji** nie mogą wprowadzać zmian w szablonach urządzeń i nie mogą administrować aplikacji. Operatory mogą dodawać i usuwać urządzenia, zarządzać zestawami urządzeń oraz uruchamiać analizy i zadania. Operatory nie będą miały dostępu do stron programu **Application Builder** i **administrowania** .

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać użytkownikami i rolami w usłudze Azure IoT Central, sugerowanym następnym krokiem jest zapoznanie się z informacjami na temat [wyświetlania rachunku](howto-view-bill.md) na platformie Azure IoT Central.
