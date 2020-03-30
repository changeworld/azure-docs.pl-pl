---
title: Integracja usługi Azure Active Directory dla usługi Azure Red Hat OpenShift
description: Dowiedz się, jak utworzyć grupę zabezpieczeń usługi Azure AD i użytkownika do testowania aplikacji w klastrze OpenShift Red Hat platformy Microsoft Azure.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: a2eade6c5a9c826d28d435a09861ba58463ae8c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280536"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integracja usługi Azure Active Directory dla usługi Azure Red Hat OpenShift

Jeśli nie utworzono jeszcze dzierżawy usługi Azure Active Directory (Azure AD), postępuj zgodnie ze wskazówkami w [obszarze Tworzenie dzierżawy usługi Azure AD dla usługi Azure Red Hat OpenShift](howto-create-tenant.md) przed kontynuowaniem tych instrukcji.

Usługa Microsoft Azure Red Hat OpenShift wymaga uprawnień do wykonywania zadań w imieniu klastra. Jeśli twoja organizacja nie ma jeszcze użytkownika usługi Azure AD, grupy zabezpieczeń usługi Azure AD lub rejestracji aplikacji usługi Azure AD do użycia jako podmiot usługi, wykonaj te instrukcje, aby je utworzyć.

## <a name="create-a-new-azure-active-directory-user"></a>Tworzenie nowego użytkownika usługi Azure Active Directory

W [witrynie Azure portal](https://portal.azure.com)upewnij się, że dzierżawa jest wyświetlana pod nazwą użytkownika w prawym górnym rogu portalu:

![Zrzut ekranu przedstawiający portal](./media/howto-create-tenant/tenant-callout.png) z dzierżawą wymienioną w prawym górnym rogu Jeśli jest wyświetlana niewłaściwa dzierżawa, kliknij nazwę użytkownika w prawym górnym rogu, a następnie kliknij pozycję **Przełącz katalog**i wybierz właściwą dzierżawę z listy **Wszystkie katalogi.**

Utwórz nowego globalnego administratora usługi Azure Active Directory, aby zalogować się do klastra Usługi Azure Red Hat OpenShift.

1. Przejdź do [bloku Użytkownicy—Wszyscy użytkownicy.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)
2. Kliknij **pozycję +Nowy użytkownik,** aby otworzyć okienko **Użytkownik.**
3. Wprowadź **nazwę** dla tego użytkownika.
4. Utwórz **nazwę użytkownika** na podstawie nazwy utworzonej `.onmicrosoft.com` dzierżawy, z dołączeną na końcu. Na przykład `yourUserName@yourTenantName.onmicrosoft.com`. Zapisz tę nazwę użytkownika. Musisz go zalogować, aby zalogować się do klastra.
5. Kliknij **pozycję Rola katalogu,** aby otworzyć okienko roli katalogu, a następnie wybierz pozycję **Administrator globalny,** a następnie kliknij przycisk **Ok** u dołu okienka.
6. W okienku **Użytkownik** kliknij pozycję **Pokaż hasło** i zarejestruj hasło tymczasowe. Po zalogowaniu się po raz pierwszy zostanie wyświetlony monit o jego zresetowanie.
7. U dołu okienka kliknij przycisk **Utwórz,** aby utworzyć użytkownika.

## <a name="create-an-azure-ad-security-group"></a>Tworzenie grupy zabezpieczeń usługi Azure AD

Aby udzielić dostępu administratora klastra, członkostwa w grupie zabezpieczeń usługi Azure AD są synchronizowane z grupą OpenShift "osa-customer-admins". Jeśli nie zostanie określony, nie zostanie przyznany dostęp administratora klastra.

1. Otwórz blok [grup usługi Azure Active Directory.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)
2. Kliknij **+Nowa grupa**.
3. Podaj nazwę i opis grupy.
4. Ustaw **typ grupy** na **Zabezpieczenia**.
5. Ustaw **typ członkostwa** na **Przypisany**.

    Dodaj użytkownika usługi Azure AD utworzonego we wcześniejszym kroku do tej grupy zabezpieczeń.

6. Kliknij pozycję **Członkowie,** aby otworzyć okienko **Wybierz elementy członkowskie.**
7. Na liście członków wybierz użytkownika usługi Azure AD, który został utworzony powyżej.
8. U dołu portalu kliknij pozycję **Wybierz,** a następnie **utwórz,** aby utworzyć grupę zabezpieczeń.

    Zapisz wartość identyfikatora grupy.

9. Po utworzeniu grupy zostanie wyświetlona na liście wszystkich grup. Kliknij nową grupę.
10. Na wyświetlona strona skopiuj identyfikator **obiektu**. Będziemy odwoływać się `GROUPID` do tej wartości, jak w [tworzenie narzędzia Azure Red Hat OpenShift klastra](tutorial-create-cluster.md) samouczek.

> [!IMPORTANT]
> Aby zsynchronizować tę grupę z grupą OpenShift osa-customer-admins, utwórz klaster przy użyciu interfejsu wiersza polecenia platformy Azure. Portal Azure obecnie brakuje pola, aby ustawić tę grupę.

## <a name="create-an-azure-ad-app-registration"></a>Tworzenie rejestracji aplikacji usługi Azure AD

Można automatycznie utworzyć klienta rejestracji aplikacji usługi Azure Active Directory (Azure AD) w `--aad-client-app-id` ramach tworzenia `az openshift create` klastra, pomijając flagę do polecenia. W tym samouczku pokazano, jak utworzyć rejestrację aplikacji usługi Azure AD w celu uzupełnienia.

Jeśli twoja organizacja nie ma jeszcze rejestracji aplikacji usługi Azure Active Directory (Azure AD) do użycia jako podmiot usługi, wykonaj te instrukcje, aby je utworzyć.

1. Otwórz [blok Rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) i kliknij **+Nowa rejestracja**.
2. W okienku **Rejestrowanie aplikacji** wprowadź nazwę rejestracji aplikacji.
3. Upewnij się, że w obszarze **Obsługiwane typy kont** są **zaznaczone tylko konta w tym katalogu organizacyjnym.** Jest to najbezpieczniejszy wybór.
4. Dodamy identyfikator URI przekierowania później, gdy poznamy identyfikator URI klastra. Kliknij przycisk **Zarejestruj,** aby utworzyć rejestrację aplikacji usługi Azure AD.
5. Na wyświetlona strona skopiuj identyfikator **aplikacji (klienta).** Będziemy odwoływać się `APPID` do tej wartości, jak w [tworzenie narzędzia Azure Red Hat OpenShift klastra](tutorial-create-cluster.md) samouczek.

![Zrzut ekranu przedstawiający stronę obiektu aplikacji](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta

Generowanie klucza tajnego klienta do uwierzytelniania aplikacji w usłudze Azure Active Directory.

1. W sekcji **Zarządzanie** na stronie rejestracje aplikacji kliknij pozycję **Certyfikaty & wpisy tajne**.
2. W okienku **Certyfikaty & wpisy tajne** kliknij pozycję **+Nowy klucz tajny klienta**.  Pojawi się okienko **Klucz tajny dodaj klienta.**
3. Podaj **opis**.
4. Ustaw **Wygasa** do czasu, który wolisz, na przykład **w 2 lata**.
5. Kliknij **przycisk Dodaj,** a wartość klucza pojawi się w sekcji **Wpisy tajne klienta** na stronie.
6. Skopiuj w dół wartość klucza. Będziemy odwoływać się `SECRET` do tej wartości, jak w [tworzenie narzędzia Azure Red Hat OpenShift klastra](tutorial-create-cluster.md) samouczek.

![Zrzut ekranu przedstawiający okienko certyfikaty i wpisy tajne](./media/howto-create-tenant/create-key.png)

Aby uzyskać więcej informacji na temat obiektów aplikacji platformy Azure, zobacz [Obiekty głównej aplikacji i usługi w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Aby uzyskać szczegółowe informacje na temat tworzenia nowej aplikacji usługi Azure AD, zobacz [Rejestrowanie aplikacji w punkcie końcowym usługi Azure Active Directory w wersji 1.0.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="add-api-permissions"></a>Dodawanie uprawnień do interfejsu API

[//]: # (Nie należy zmieniać na Microsoft Graph. Nie działa z programem Microsoft Graph.)
1. W sekcji **Zarządzanie** kliknij pozycję **Uprawnienia interfejsu API**
2. Kliknij **pozycję Dodaj uprawnienia** i wybierz pozycję Wykres usługi Azure Active **Directory,** a następnie **uprawnienia delegowane**.
> [!NOTE]
> Upewnij się, że wybrano kafelek "Azure Active Directory Graph", a nie "Microsoft Graph".

3. Rozwiń **użytkownika** na poniższej liście i włącz uprawnienie **User.Read.** Jeśli **User.Read** jest domyślnie włączony, upewnij się, że jest to uprawnienie **User.Read**usługi **Azure Active Directory.**
4. Przewiń w górę i wybierz pozycję **Uprawnienia aplikacji**.
5. Rozwiń **katalog** na poniższej liście i włącz **program Directory.ReadAll**.
6. Kliknij **pozycję Dodaj uprawnienia,** aby zaakceptować zmiany.
7. Panel uprawnień interfejsu API powinien teraz wyświetlać zarówno *user.read,* jak i *Directory.ReadAll*. Należy pamiętać, ostrzeżenie w **kolumnie wymagana zgoda administratora** obok *Katalogu.ReadAll*.
8. Jeśli jesteś *administratorem subskrypcji platformy Azure,* kliknij przycisk **Udziel zgody administratora dla *nazwy subskrypcji* ** poniżej. Jeśli nie jesteś *administratorem subskrypcji platformy Azure,* poproś o zgodę administratora.

![Zrzut ekranu przedstawiający panel uprawnień interfejsu API. User.Read i Directory.ReadWszystko dodane uprawnienia, zgoda administratora wymagana dla Katalogu.ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> Synchronizacja grupy administratorów klastra będzie działać dopiero po udzieleniu zgody. W kolumnie Wymagana *zgoda administratora* zostanie wyświetlone zielone kółko z znacznikiem wyboru i komunikatem "Przyznane *dla nazwy subskrypcji".*

Aby uzyskać szczegółowe informacje na temat zarządzania administratorami i innymi rolami, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).

## <a name="resources"></a>Resources

* [Obiekty głównej aplikacji i usługi w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Szybki start: rejestrowanie aplikacji z punktem końcowym usługi Azure Active Directory w wersji 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>Następne kroki

Jeśli spełnisz wszystkie [wymagania wstępne usługi Azure Red Hat OpenShift,](howto-setup-environment.md)możesz przystąpić do tworzenia pierwszego klastra!

Wypróbuj samouczek:
> [!div class="nextstepaction"]
> [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md)
