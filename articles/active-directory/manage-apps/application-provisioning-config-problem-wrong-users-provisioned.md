---
title: Proces aprowizacji obejmuje nieprawidłowy zbiór użytkowników do aplikacji galerii usługi Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można dowiedzieć się, dlaczego inny zbiór użytkowników są aprowizowane do aplikacji, niż te, które miały
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1727c45378e36abf695fca32c6e630806b4a6e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784506"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Proces aprowizacji obejmuje nieprawidłowy zbiór użytkowników do aplikacji galerii usługi Azure AD

Użytkowników, którzy są udostępnione do aplikacji, przede wszystkim jest wymuszany przez zostały którym użytkownikom i grupom **przypisane** do aplikacji.

Użyj następujących zasobów, aby dowiedzieć się, jak sprawdzić, którzy użytkownicy i grupy zostały przypisane do aplikacji w usłudze Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Przypisywanie użytkownika bezpośrednio z uprawnieniami administratora

Aby przypisać jeden lub więcej użytkowników aplikacji bezpośrednio, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7. Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** menu nawigacji po lewej stronie aplikacji.

8. Aby otworzyć **Dodaj przydziału** okienku kliknij **Dodaj** przycisk w górnej części **użytkowników i grup** listy.

9. Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Pełna nazwa** lub **adres e-mail** użytkownika, jesteś zainteresowany przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok logo, aby dodać użytkownika, aby lub zdjęcie w profilu użytkownika **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, typ w innym **Pełna nazwa** lub **adres e-mail** do **wyszukiwanie według nazwy lub adresu e-mail** pole wyszukiwania, a następnie kliknij pole wyboru, aby dodać użytkownika do **wybrane** listy.

13. Gdy to zrobisz, Wybieranie użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę, aby przypisać użytkownikom wybrania.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

Jeśli Inicjowanie obsługi administracyjnej jest skonfigurowana i uruchomiona już aplikację, aprowizowani będą nowi użytkownicy z aplikacją w ciągu 10 minut. Sprawdź **dzienników inspekcji** Aby uzyskać szczegółowe informacje.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Przypisania grupy bezpośrednio do aplikacji jako administrator

Aby przypisać co najmniej jedną grupę aplikacji bezpośrednio, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7. Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** menu nawigacji po lewej stronie aplikacji.

8. Aby otworzyć **Dodaj przydziału** okienku kliknij **Dodaj** przycisk w górnej części **użytkowników i grup** listy.

9. Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Nazwa grupy pełną** grupy osób zainteresowanych przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **grupy** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok zdjęcie w profilu lub logo, aby dodać użytkownika do grupy **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednej grupy**, typ w innym **Nazwa grupy pełną** do **wyszukiwanie według nazwy lub adresu e-mail** polu wyszukiwania, a następnie kliknij pole wyboru, aby dodać tę grupę Aby **wybrane** listy.

13. Po wybraniu grup kliknij **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę do przypisania do wybranych grupach.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych grup.

Jeśli Inicjowanie obsługi administracyjnej jest skonfigurowany i uruchomiony dla aplikacji, nowych użytkowników znajdujących się w grupie powinny zostać aprowizowane do aplikacji w ciągu 10 minut. Sprawdź **dzienników inspekcji** Aby uzyskać szczegółowe informacje.

>[!IMPORTANT]
>Inicjowanie obsługi nazwy grupy i szczegóły grupy, oprócz członków, jeśli jest obsługiwany dla niektórych aplikacji. Można włączyć lub wyłączyć tę funkcję, włączając lub wyłączając **mapowanie** dla obiektów grupy wyświetlane w **aprowizacji** kartę. 
>
>

Jeśli Inicjowanie obsługi administracyjnej grupy jest włączona, należy przejrzeć mapowania atrybutów, aby upewnić się, że odpowiednie pole jest on używany do dopasowywania "ID". Ten identyfikator dopasowania mogą być wyświetlaną nazwę lub alias e-mail. Grupy i jej elementów członkowskich nie są udostępnione, jeśli właściwość dopasowania jest pusty lub nie jest wypełnione dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Kolejne kroki
[Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory](user-provisioning.md)
