---
title: Niewłaściwy zestaw użytkowników jest zainicjowany do aplikacji w galerii usługi Azure AD
description: Dowiedz się, jak dowiedzieć się, dlaczego do aplikacji jest inicjowany inny zestaw użytkowników niż oczekiwano
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
ms.openlocfilehash: 77e8f31bd0adeabea79cd11553fd77a3e5ab6404
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275772"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>W aplikacji z galerii usługi Azure AD jest inicjowany niewłaściwy zestaw użytkowników

Użytkowników, którzy są obsługiwani do aplikacji, są przede wszystkim oparte na tym, którzy użytkownicy i które grupy zostali **przypisani** do aplikacji.

Skorzystaj z następujących zasobów, aby dowiedzieć się, jak sprawdzić, którzy użytkownicy i które grupy zostali przypisani do aplikacji w ramach Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Przypisywanie użytkownika bezpośrednio jako administrator

Aby bezpośrednio przypisać jednego lub większą liczbę użytkowników do aplikacji, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

8. Aby otworzyć okienko **Dodaj przypisanie** , kliknij przycisk **Dodaj** na górze listy **Użytkownicy i grupy** .

9. Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Pełna nazwa** lub **adres e-mail** użytkownika, jesteś zainteresowany przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok logo, aby dodać użytkownika, aby lub zdjęcie w profilu użytkownika **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, typ w innym **Pełna nazwa** lub **adres e-mail** do **wyszukiwanie według nazwy lub adres e-mail** polu wyszukiwania, a następnie kliknij pole wyboru, aby dodać użytkownika do **wybrane** listy.

13. Gdy to zrobisz, Wybieranie użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę, aby przypisać użytkownikom wybrania.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

Jeśli obsługa administracyjna została skonfigurowana i jest już uruchomiona dla aplikacji, nowi użytkownicy powinni być obsługiwani do aplikacji w ciągu około 10 minut. Szczegółowe informacje znajdują się w **dziennikach inspekcji** .

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Przypisywanie grupy bezpośrednio do aplikacji jako administrator

Aby bezpośrednio przypisać jedną lub więcej grup do aplikacji, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

8. Aby otworzyć okienko **Dodaj przypisanie** , kliknij przycisk **Dodaj** na górze listy **Użytkownicy i grupy** .

9. Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Nazwa grupy pełną** grupy osób zainteresowanych przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **grupy** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok zdjęcie w profilu lub logo, aby dodać użytkownika do grupy **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednej grupy**, typ w innym **grupy pełną nazwę** do **wyszukiwanie według nazwy lub adresu e-mail** pole wyszukiwania i Kliknij pole wyboru, aby dodać tę grupę do **wybrane** listy.

13. Po wybraniu grup kliknij **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę do przypisania do wybranych grupach.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych grup.

Jeśli obsługa administracyjna została skonfigurowana i jest już uruchomiona dla aplikacji, w ciągu około 10 minut należy zainicjować obsługę administracyjną w aplikacji nowych użytkowników należących do tej grupy. Szczegółowe informacje znajdują się w **dziennikach inspekcji** .

>[!IMPORTANT]
>Inicjowanie obsługi administracyjnej nazw grup i grup, oprócz członków, jeśli są obsługiwane w przypadku niektórych aplikacji. Tę funkcję można włączyć lub wyłączyć, włączając lub wyłączając **Mapowanie** dla obiektów grupy wyświetlanych na karcie **aprowizacji** . 
>
>

Jeśli włączono grupy aprowizacji, należy sprawdzić mapowania atrybutów, aby upewnić się, że odpowiednie pole jest używane dla "zgodnego identyfikatora". Ten pasujący Identyfikator może być nazwą wyświetlaną lub aliasem adresu e-mail. Grupa i jej elementy członkowskie nie są obsługiwane, jeśli właściwość pasująca jest pusta lub nie została wypełniona dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki
[Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory](user-provisioning.md)
