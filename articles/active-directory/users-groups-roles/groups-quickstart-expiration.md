---
title: 'Szybki start: zasady wygasania grup usługi Office 365 w usłudze Azure Active Directory | Microsoft Docs'
description: Wygasanie grup usługi Office 365 — Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: quickstart
ms.date: 08/07/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 7008943e9077cbad3c58de43f64b105f35931bf3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "40208930"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Szybki start: konfigurowanie wygasania grup usługi Office 365 w usłudze Azure Active Directory

W tym przewodniku Szybki start ustawisz zasady wygasania grup usługi Office 365. Umożliwienie użytkownikom tworzenia własnych grup może prowadzić do powstania wielu nieużywanych grup. Jednym ze sposobów radzenia sobie z nieużywanymi grupami jest skonfigurowanie wygasania tych grup, co pozwala uniknąć konieczności ich ręcznego usuwania.

Zasady wygasania są proste:

* Właściciele grupy są powiadamiani o konieczności odnowienia wygasającej grupy
* Grupa, która nie zostanie odnowiona, zostanie usunięta
* Usunięta grupa usługi Office 365 może zostać przywrócona w ciągu 30 dni przez właściciela grupy lub administratora usługi Azure AD

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisite"></a>Wymagania wstępne

Musisz być administratorem globalnym lub administratorem kont użytkowników w dzierżawie, aby skonfigurować wygasanie grup.

## <a name="turn-on-user-creation-for-groups"></a>Włączanie tworzenia grup przez użytkowników

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego lub administratora kont użytkowników katalogu.

2. Wybierz pozycję **Grupy**, a następnie wybierz pozycję **Ogólne**.
  
  ![Samoobsługowe ustawienia grup](./media/groups-quickstart-expiration/self-service-settings.png)

3. Zmień wartość ustawienia **Użytkownicy mogą tworzyć grupy usługi Office 365** na **Tak**.

4. Wybierz pozycję **Zapisz**, aby zapisać ustawienia grup, gdy wszytko będzie gotowe.

## <a name="set-group-expiration"></a>Ustawianie czasu wygaśnięcia grup

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Azure Active Directory** > **Grupy** > **Wygasanie**, aby otworzyć ustawienia wygasania.
  
  ![Ustawienia wygasania](./media/groups-quickstart-expiration/expiration-settings.png)

2. Ustaw interwał wygasania. Wybierz wstępnie zdefiniowaną wartość lub wprowadź niestandardową wartość większą niż 31 dni. 

3. Podaj adres e-mail, na który mają być wysyłane powiadomienia dotyczące wygaśnięcia, jeśli grupa nie ma właściciela.

4. Na potrzeby tego podręcznika Szybki start ustaw opcję **Włącz wygaśnięcie dla tych grup usługi Office 365** na wartość **Wszystkie**.

5. Wybierz przycisk **Zapisz**, aby zapisać ustawienia wygasania, gdy wszystko będzie gotowe.

Gotowe. W tym przewodniku Szybki start pomyślnie ustawiono zasady wygasania dla wybranych grup usługi Office 365.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

**Aby usunąć zasady wygasania**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dzierżawy.
2. Wybierz pozycję **Azure Active Directory** > **Grupy** > **Wygasanie**.
3. Ustaw opcję **Włącz wygaśnięcie dla tych grup usługi Office 365** na wartość **Brak**.

**Aby wyłączyć tworzenie grup przez użytkowników**

1. Wybierz pozycję **Azure Active Directory** > **Grupy** > **Ogólne**. 
2. Ustaw opcję **Użytkownicy mogą tworzyć grupy usługi Office 365 w portalach platformy Azure** na wartość **Nie**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wygasania, w tym na temat ograniczeń technicznych, dodawania listy niestandardowych słów zablokowanych oraz środowiska użytkownika końcowego w aplikacjach usługi Office 365, zapoznaj się z następującym artykułem zawierającym szczegółowe informacje na temat zasad wygasania:

> [!div class="nextstepaction"]
> [Expiration policy all details (Wszystkie szczegóły dotyczące zasad wygasania)](groups-lifecycle.md)