---
title: 'Samouczek: Konfigurowanie concur do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory| Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Concur.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441aa9805f2a453e22f207238315125d2a281838
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60280402"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie konkursu do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroki, które należy wykonać w Concur i usługi Azure AD, aby automatycznie aprowizować i usuwać z aprowizowania konta użytkowników z usługi Azure AD do Concur.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

*   Dzierżawa usługi Azure Active directory.
*   A Concur subskrypcji z włączoną rejestracją jednokrotną.
*   Konto użytkownika w concur z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-concur"></a>Przypisywanie użytkowników do concur

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD jest synchronizowana.

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Concur. Po podjęciu decyzji, można przypisać tych użytkowników do aplikacji Concur, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Concur

*   Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Concur, aby przetestować konfigurację inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

*   Podczas przypisywania użytkownika do concur, należy wybrać prawidłową rolę użytkownika. Rola "Dostęp domyślny" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-user-provisioning"></a>Włączanie inicjowania obsługi administracyjnej użytkowników

W tej sekcji przewodnik po połączeniu usługi Azure AD z interfejsem API inicjowania obsługi administracyjnej konta użytkownika concur i konfigurowaniu usługi inicjowania obsługi administracyjnej w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w concur na podstawie przypisania użytkownika i grupy w usłudze Azure AD.

> [!Tip] 
> Można również włączyć samoostiło samol oparte na logacie jednokrotne dla concur, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal.](https://portal.azure.com) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-user-account-provisioning"></a>Aby skonfigurować inicjowanie obsługi administracyjnej kont użytkowników:

Celem tej sekcji jest określenie sposobu włączania obsługi administracyjnej kont użytkowników usługi Active Directory do concur.

Aby włączyć aplikacje w usłudze wydatków, musi istnieć właściwa konfiguracja i korzystanie z profilu administratora usługi sieci Web. Nie dodawaj roli Administrator WS do istniejącego profilu administratora używanego w funkcjach administracyjnych T&E.

Concur Consultants lub administrator klienta muszą utworzyć odrębny profil administratora usługi sieci Web, a administrator klienta musi używać tego profilu dla funkcji administratora usług sieci Web (na przykład włączanie aplikacji). Profile te muszą być oddzielone od codziennego profilu administratora klienta T&E (profil administratora T&E nie powinien mieć przypisanej roli WSAdmin).

Podczas tworzenia profilu, który ma być używany do włączania aplikacji, wprowadź nazwę administratora klienta w polach profilu użytkownika. Spowoduje to przypisanie własności do profilu. Po utworzeniu jednego lub więcej profili klient musi zalogować się za pomocą tego profilu, aby kliknąć przycisk *"Włącz"* aplikacji partnera w menu Usługi sieci Web.

Z następujących powodów, to działanie nie powinno być wykonywane z profilem, którego używają do normalnego administracji T&E.

* Klient musi być tym, który klika *"Tak"* w oknie dialogowym, które jest wyświetlane po włączeniu aplikacji. To kliknięcie potwierdza, że klient jest skłonny do aplikacji Partnera, aby uzyskać dostęp do swoich danych, więc ty lub partner nie można kliknąć tego przycisku Tak.

* Jeśli administrator klienta, który włączył aplikację przy użyciu profilu administratora T&E, opuści firmę (co powoduje dezaktywowanie profilu), wszystkie aplikacje włączone przy użyciu tego profilu nie będą działać, dopóki aplikacja nie zostanie włączona z innym aktywnym profilem administratora WS. Dlatego masz tworzyć różne profile administratorów WS.

* Jeśli administrator opuści firmę, nazwę skojarzoną z profilem administratora WS można zmienić na administratora zastępczego w razie potrzeby bez wpływu na włączoną aplikację, ponieważ ten profil nie wymaga dezaktywacji.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do **dzierżawy Concur.**

2. Z menu **Administracja** wybierz polecenie **Usługi sieci Web**.
   
    ![Zbieżny najemca](./media/concur-provisioning-tutorial/IC721729.png "Zbieżny najemca")

3. Po lewej stronie w okienku **Usługi sieci Web** wybierz pozycję Włącz aplikację **partnera**.
   
    ![Włącz aplikację partnera](./media/concur-provisioning-tutorial/ic721730.png "Włącz aplikację partnera")

4. Z listy **Włącz aplikację** wybierz pozycję Azure **Active Directory**, a następnie kliknij pozycję **Włącz**.
   
    ![Usługa Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Usługi Active Directory systemu Microsoft Azure")

5. Kliknij **przycisk Tak,** aby zamknąć okno dialogowe **Potwierdź akcję.**
   
    ![Potwierdź akcję](./media/concur-provisioning-tutorial/ic721732.png "Potwierdź akcję")

6. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

7. Jeśli skonfigurowano już concur dla logowania jednokrotnego, wyszukaj wystąpienie Concur przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj **concur** w galerii aplikacji. Wybierz pozycję Zbieżn z wyników wyszukiwania i dodaj je do listy aplikacji.

8. Wybierz wystąpienie Concur, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

9. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**. 
 
    ![Inicjowania obsługi](./media/concur-provisioning-tutorial/provisioning.png)

10. W sekcji **Poświadczenia administratora** wprowadź **nazwę użytkownika** i **hasło** administratora concur.

11. W witrynie Azure portal kliknij pozycję **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z aplikacją Concur. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Concur ma uprawnienia administratora zespołu.

12. Wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej w polu **Wiadomości e-mail powiadomień,** i zaznacz pole wyboru.

13. Kliknij **pozycję Zapisz.**

14. W sekcji Mapowania wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory do concur.**

15. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Concur. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Concur dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

16. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla concur, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia**

17. Kliknij **pozycję Zapisz.**

Teraz możesz utworzyć konto testowe. Poczekaj do 20 minut, aby sprawdzić, czy konto zostało zsynchronizowane z Concur.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](concur-tutorial.md)

