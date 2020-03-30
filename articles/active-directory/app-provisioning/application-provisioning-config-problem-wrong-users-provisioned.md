---
title: Niewłaściwy zestaw użytkowników są aprowied do aplikacji usługi Azure AD Gallery
description: Dowiedz się, jak dowiedzieć się, dlaczego inny zestaw użytkowników jest aprowizowany do aplikacji niż oczekiwano
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c94388011605da73666e82011bb8ef56d2af8d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522785"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Niewłaściwy zestaw użytkowników jest aprowiany do aplikacji usługi Azure AD Gallery

Użytkownicy, którzy są aprowied do aplikacji jest przede wszystkim napędzany przez użytkowników i grup zostały **przypisane** do aplikacji.

Skorzystaj z następujących zasobów, aby dowiedzieć się, jak sprawdzić, którzy użytkownicy i grupy zostały przypisane do aplikacji w usłudze Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Przypisywanie użytkownika bezpośrednio jako administratora

Aby bezpośrednio przypisać jednego lub więcej użytkowników do aplikacji, wykonaj następujące kroki:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** z menu nawigacji po lewej stronie aplikacji.

8. Aby otworzyć okienko **Dodaj przydział,** kliknij przycisk **Dodaj** u góry listy **Użytkownicy i grupy.**

9. kliknij selektor **Użytkownicy i grupy** w okienku **Dodawanie przypisania.**

10. Wpisz **pełną nazwę** lub **adres e-mail** użytkownika, którego jesteś zainteresowany, aby przypisać do pola wyszukiwania według nazwy lub adresu **e-mail.**

11. Umieść wskaźnik myszy na **użytkowniku** na liście, aby wyświetlić **pole wyboru**. Kliknij pole wyboru obok zdjęcia profilowego lub logo użytkownika, aby dodać go do **wybranej** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika,** wpisz inne imię i **nazwisko** lub **adres e-mail** w polu wyszukiwania według nazwy lub adresu **e-mail,** a następnie kliknij to pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników kliknij przycisk **Wybierz,** aby dodać ich do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **wybierz rolę** w okienku **Dodaj przypisanie,** aby wybrać rolę przypisaną do wybranych użytkowników.

15. Kliknij przycisk **Przypisz,** aby przypisać aplikację do wybranych użytkowników.

Jeśli inicjowanie obsługi administracyjnej jest skonfigurowany i już uruchomiony dla aplikacji, nowi użytkownicy powinny być aprowizowane do aplikacji w około 10 minut. Sprawdź **dzienniki inspekcji, aby** uzyskać szczegółowe informacje.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Przypisywanie grupy bezpośrednio do aplikacji jako administratora

Aby bezpośrednio przypisać jedną lub więcej grup do aplikacji, wykonaj następujące kroki:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** z menu nawigacji po lewej stronie aplikacji.

8. Aby otworzyć okienko **Dodaj przydział,** kliknij przycisk **Dodaj** u góry listy **Użytkownicy i grupy.**

9. kliknij selektor **Użytkownicy i grupy** w okienku **Dodawanie przypisania.**

10. Wpisz **pełną nazwę grupy,** którą chcesz przypisać do pola wyszukiwania **według nazwy lub adresu e-mail.**

11. Umieść wskaźnik myszy na **grupie** na liście, aby wyświetlić **pole wyboru**. Kliknij pole wyboru obok zdjęcia profilowego lub logo grupy, aby dodać go do **wybranej** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jedną grupę,** wpisz inną pełną nazwę **grupy** w polu wyszukiwania według nazwy lub **adresu e-mail,** a następnie kliknij to pole wyboru, aby dodać tę grupę do **wybranej** listy.

13. Po zakończeniu wybierania grup kliknij przycisk **Wybierz,** aby dodać je do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **wybierz rolę** w okienku **Dodaj przypisanie,** aby wybrać rolę przypisaną do wybranych grup.

15. Kliknij przycisk **Przypisz,** aby przypisać aplikację do wybranych grup.

Jeśli inicjowanie obsługi administracyjnej jest skonfigurowany i już uruchomiony dla aplikacji, nowych użytkowników zawartych w grupie powinny być aprowizowane do aplikacji w ciągu około 10 minut. Sprawdź **dzienniki inspekcji, aby** uzyskać szczegółowe informacje.

>[!IMPORTANT]
>Inicjowanie obsługi administracyjnej nazwy grupy i szczegółów grupy, oprócz członków, jeśli są obsługiwane dla niektórych aplikacji. Tę funkcję można włączyć lub wyłączyć, włączając lub wyłączając **mapowanie** obiektów grupy wyświetlanych na karcie **Inicjowanie obsługi administracyjnej.** 
>
>

Jeśli grupy inicjowania obsługi administracyjnej jest włączona, należy przejrzeć mapowania atrybutów, aby upewnić się, że odpowiednie pole jest używane dla "pasujące identyfikator". Ten pasujący identyfikator może być wyświetlaną nazwą lub aliasem e-mail. Grupa i jej członkowie nie są aprowiowane, jeśli pasująca właściwość jest pusta lub nie wypełniona dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki
[Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory](user-provisioning.md)
