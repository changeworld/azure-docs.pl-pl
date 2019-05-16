---
title: 'Szybki start: zasady wygasania grup usługi Office 365 — Azure Active Directory | Microsoft Docs'
description: Wygasanie grup usługi Office 365 — Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01cedadc115496fcf00df986b4ad4b9c5aab5139
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606173"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Szybki start: Konfigurowanie wygasania grup usługi Office 365 w usłudze Azure Active Directory

W tym przewodniku Szybki start ustawisz zasady wygasania grup usługi Office 365. Umożliwienie użytkownikom tworzenia własnych grup może prowadzić do powstania wielu nieużywanych grup. Jednym ze sposobów radzenia sobie z nieużywanymi grupami jest skonfigurowanie wygasania tych grup, co pozwala uniknąć konieczności ich ręcznego usuwania.

Zasady wygasania są proste:

* Właściciele grupy są powiadamiani o konieczności odnowienia wygasającej grupy
* Grupa, która nie zostanie odnowiona, zostanie usunięta
* Usunięta grupa usługi Office 365 może zostać przywrócona w ciągu 30 dni przez właściciela grupy lub administratora usługi Azure AD

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisite"></a>Wymaganie wstępne

 Rola najmniej uprzywilejowane, wymagane do skonfigurowania wygaśnięcie grupy jest administrator użytkowników w organizacji.

## <a name="turn-on-user-creation-for-groups"></a>Włączanie tworzenia grup przez użytkowników

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora użytkowników.

2. Wybierz pozycję **Grupy**, a następnie wybierz pozycję **Ogólne**.
  
   ![Strona Ustawienia grupami samoobsługi](./media/groups-quickstart-expiration/self-service-settings.png)

3. Zmień wartość ustawienia **Użytkownicy mogą tworzyć grupy usługi Office 365** na **Tak**.

4. Wybierz pozycję **Zapisz**, aby zapisać ustawienia grup, gdy wszytko będzie gotowe.

## <a name="set-group-expiration"></a>Ustawianie czasu wygaśnięcia grup

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), wybierz opcję **usługi Azure Active Directory** > **grup** > **wygaśnięcia** do Otwórz ustawienia wygaśnięcia.
  
   ![Strona ustawienia wygaśnięcia dla grupy](./media/groups-quickstart-expiration/expiration-settings.png)

2. Ustaw interwał wygasania. Wybierz wstępnie zdefiniowaną wartość lub wprowadź niestandardową wartość większą niż 31 dni. 

3. Podaj adres e-mail, na który mają być wysyłane powiadomienia dotyczące wygaśnięcia, jeśli grupa nie ma właściciela.

4. Na potrzeby tego podręcznika Szybki start ustaw opcję **Włącz wygaśnięcie dla tych grup usługi Office 365** na wartość **Wszystkie**.

5. Wybierz przycisk **Zapisz**, aby zapisać ustawienia wygasania, gdy wszystko będzie gotowe.

To wszystko! W tym przewodniku Szybki start pomyślnie ustawiono zasady wygasania dla wybranych grup usługi Office 365.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

### <a name="to-remove-the-expiration-policy"></a>Aby usunąć zasady wygasania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dzierżawy.
2. Wybierz pozycję **Azure Active Directory** > **Grupy** > **Wygasanie**.
3. Ustaw opcję **Włącz wygaśnięcie dla tych grup usługi Office 365** na wartość **Brak**.

### <a name="to-turn-off-user-creation-for-groups"></a>Aby wyłączyć tworzenie użytkownika w grupach

1. Wybierz pozycję **Azure Active Directory** > **Grupy** > **Ogólne**. 
2. Ustaw opcję **Użytkownicy mogą tworzyć grupy usługi Office 365 w portalach platformy Azure** na wartość **Nie**.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat wygaśnięcia, w tym instrukcje dotyczące programu PowerShell i ograniczenia techniczne zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Zasady wygasania programu PowerShell](groups-lifecycle.md)
