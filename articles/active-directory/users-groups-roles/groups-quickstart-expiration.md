---
title: Szybki start zasad wygasania grup — usługa Azure AD | Dokumenty firmy Microsoft
description: Wygasanie grup usługi Office 365 — Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16b6e4f521568c89e415e6e9fa621175052cf34d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74026895"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Szybki start: konfigurowanie wygasania grup usługi Office 365 w usłudze Azure Active Directory

W tym przewodniku Szybki start ustawisz zasady wygasania grup usługi Office 365. Umożliwienie użytkownikom tworzenia własnych grup może prowadzić do powstania wielu nieużywanych grup. Jednym ze sposobów radzenia sobie z nieużywanymi grupami jest skonfigurowanie wygasania tych grup, co pozwala uniknąć konieczności ich ręcznego usuwania.

Zasady wygasania są proste:

- Grupy z działaniami użytkowników są automatycznie odnawiane w miarę zbliżania się wygaśnięcia
- Właściciele grupy są powiadamiani o konieczności odnowienia wygasającej grupy
- Grupa, która nie zostanie odnowiona, zostanie usunięta
- Usunięta grupa usługi Office 365 może zostać przywrócona w ciągu 30 dni przez właściciela grupy lub administratora usługi Azure AD

> [!NOTE]
> Grupy używają teraz analizy usługi Azure AD do automatycznego odnawiania na podstawie tego, czy były używane w ostatnim czasie. Ta decyzja o odnowieniu jest oparta na aktywności użytkowników w grupach w usługach Office 365, takich jak Outlook, SharePoint, Teams, Yammer i innych.

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisite"></a>Wymagania wstępne

 Najmniej uprzywilejowaną rolą wymaganą do skonfigurowania wygaśnięcia grupy jest administrator użytkownika w organizacji.

## <a name="turn-on-user-creation-for-groups"></a>Włączanie tworzenia grup przez użytkowników

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy za pomocą konta administratora użytkownika.

2. Wybierz pozycję **Grupy**, a następnie wybierz pozycję **Ogólne**.
  
   ![Strona ustawień grupy samoobsługowej](./media/groups-quickstart-expiration/self-service-settings.png)

3. Zmień wartość ustawienia **Użytkownicy mogą tworzyć grupy usługi Office 365** na **Tak**.

4. Wybierz pozycję **Zapisz**, aby zapisać ustawienia grup, gdy wszytko będzie gotowe.

## <a name="set-group-expiration"></a>Ustawianie czasu wygaśnięcia grup

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com), wybierz**opcję Wygasanie** **grup** >  **usługi Azure Active Directory,** > aby otworzyć ustawienia wygaśnięcia.
  
   ![Strona ustawień wygaśnięcia dla grupy](./media/groups-quickstart-expiration/expiration-settings.png)

2. Ustaw interwał wygasania. Wybierz wstępnie zdefiniowaną wartość lub wprowadź niestandardową wartość większą niż 31 dni. 

3. Podaj adres e-mail, na który mają być wysyłane powiadomienia dotyczące wygaśnięcia, jeśli grupa nie ma właściciela.

4. Na potrzeby tego podręcznika Szybki start ustaw opcję **Włącz wygaśnięcie dla tych grup usługi Office 365** na wartość **Wszystkie**.

5. Wybierz przycisk **Zapisz**, aby zapisać ustawienia wygasania, gdy wszystko będzie gotowe.

Gotowe. W tym przewodniku Szybki start pomyślnie ustawiono zasady wygasania dla wybranych grup usługi Office 365.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

### <a name="to-remove-the-expiration-policy"></a>Aby usunąć zasady wygasania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dzierżawy.
2. Wybierz**wygaśnięcie****grup** >  **usługi Azure Active Directory** > .
3. Ustaw opcję **Włącz wygaśnięcie dla tych grup usługi Office 365** na wartość **Brak**.

### <a name="to-turn-off-user-creation-for-groups"></a>Aby wyłączyć tworzenie grup przez użytkowników

1. Wybierz pozycję**Grupy** > **ogólne**usługi Azure Active **Directory** > . 
2. Ustaw opcję **Użytkownicy mogą tworzyć grupy usługi Office 365 w portalach platformy Azure** na wartość **Nie**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wygaśnięcia, w tym instrukcje programu PowerShell i ograniczenia techniczne, zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Zasady wygasania programu PowerShell](groups-lifecycle.md)
