---
title: Integracja usługi Azure Active Directory dla usługi Azure Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć grupę zabezpieczeń usługi Azure AD i użytkownika do testowania aplikacji w klastrze usługi Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2019
ms.openlocfilehash: adc5a601a04936a376d7c69b26c2429940ebdf6e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306474"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integracja usługi Azure Active Directory dla usługi Azure Red Hat OpenShift

Jeśli nie utworzono już dzierżawę usługi Azure Active Directory (Azure AD), postępuj zgodnie z instrukcjami w [utworzyć dzierżawę usługi Azure AD dla usługi Azure Red Hat OpenShift](howto-create-tenant.md) przed wykonaniem tych instrukcji.

Microsoft Azure Red Hat OpenShift musi mieć uprawnienia do wykonywania zadań w imieniu klastra. Jeśli Twoja organizacja nie ma jeszcze użytkownika usługi Azure AD, grupy zabezpieczeń usługi Azure AD lub rejestracja aplikacji usługi Azure AD do użycia jako nazwa główna usługi, wykonaj te instrukcje, aby je utworzyć.

## <a name="create-a-new-azure-active-directory-user"></a>Tworzenie nowego użytkownika usługi Azure Active Directory

W [witryny Azure portal](https://portal.azure.com), upewnij się, że dzierżawy jest wyświetlany w obszarze swoją nazwę użytkownika w prawym górnym rogu portalu:

![Zrzut ekranu przedstawiający portal z dzierżawą wymienione w prawym górnym rogu](./media/howto-create-tenant/tenant-callout.png) Jeśli nieprawidłowej dzierżawy jest wyświetlany, kliknij swoją nazwę użytkownika w prawym górnym rogu, a następnie kliknij przycisk **Przełącz katalog**i wybierz poprawny dzierżawę z **wszystkie Katalogi** listy.

Utwórz nowego użytkownika administratora globalnego usługi Azure Active Directory do logowania do klastra usługi Azure Red Hat OpenShift.

1. Przejdź do [użytkowników dla wszystkich użytkowników](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) bloku.
2. Kliknij przycisk **+ nowy użytkownik** otworzyć **użytkownika** okienka.
3. Wprowadź **nazwa** dla tego użytkownika.
4. Tworzenie **nazwa_użytkownika** na podstawie nazwy dzierżawy, możesz utworzyć przy użyciu `.onmicrosoft.com` dołączany na końcu. Na przykład `yourUserName@yourTenantName.onmicrosoft.com`. Zanotuj nazwę tego użytkownika. Będzie potrzebny do logowania do klastra.
5. Kliknij przycisk **roli w katalogu** aby otworzyć okienko rolę katalogu, a następnie wybierz **administratora globalnego** a następnie kliknij przycisk **Ok** w dolnej części okienka.
6. W **użytkownika** okienku kliknij **Pokaż hasło** i Zapisz hasło tymczasowe. Po zalogowaniu się po raz pierwszy, zostanie wyświetlony monit, aby je zresetować.
7. W dolnej części okienka kliknij **Utwórz** do utworzenia użytkownika.

## <a name="create-an-azure-ad-security-group"></a>Utwórz grupę zabezpieczeń usługi Azure AD

Aby udzielić dostępu administracyjnego klastra, członkostwa w grupie zabezpieczeń usługi Azure AD są synchronizowane w OpenShift grupy "osa — klient admins". Jeśli nie zostanie określony, zostaną przyznane bez dostępu administratora klastra.

1. Otwórz [grup usługi Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) bloku.
2. Kliknij przycisk **+ Nowa grupa**
3. Podaj nazwę i opis grupy.
4. Ustaw **typ grupy** do **zabezpieczeń**.
5. Ustaw **Typ członkostwa** do **przypisane**.

    Dodaj użytkownika usługi Azure AD, który został utworzony w poprzednim kroku do tej grupy zabezpieczeń.

6. Kliknij przycisk **członków** otworzyć **Wybieranie elementów członkowskich** okienka.
7. Na liście elementów członkowskich wybierz użytkownika usługi Azure AD, który został utworzony powyżej.
8. W dolnej części portalu kliknij pozycję **wybierz** i następnie **Utwórz** Aby utworzyć grupę zabezpieczeń.

    Zanotuj wartość Identyfikatora grupy

9. Po utworzeniu grupy będzie ją widział w listę wszystkich grup. Kliknij nową grupę.
10. Na wyświetlonej stronie Skopiuj **obiektu o identyfikatorze**. Firma Microsoft będzie odnosił się do tej wartości jako `GROUPID` w [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) samouczka.

## <a name="create-an-azure-ad-app-registration"></a>Utwórz rejestrację aplikacji usługi Azure AD

Może automatycznie tworzyć klienta rejestracji aplikacji usługi Azure Active Directory (Azure AD) jako część tworzenia klastra, pomijając `--aad-client-app-id` flaga `az openshift create` polecenia. W tym samouczku przedstawiono sposób tworzenia rejestracji aplikacji usługi Azure AD, aby informacje były kompletne.

Jeśli Twoja organizacja nie ma jeszcze rejestracji aplikacji usługi Azure Active Directory (Azure AD) do użycia jako nazwy głównej usługi, wykonaj te instrukcje, aby go utworzyć.

1. Otwórz [bloku rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) i kliknij przycisk **+ rejestrowanie nowej**.
2. W **rejestrowania aplikacji** okienku, wprowadź nazwę dla rejestracji aplikacji.
3. Upewnij się, że w obszarze **obsługiwane typy kont** , **kont w tym katalogu organizacji tylko** jest zaznaczone. Jest to najbezpieczniejsza opcja wyboru.
4. Firma Microsoft doda identyfikatora URI przekierowania później, gdy wiemy, że identyfikator URI klastra. Kliknij przycisk **zarejestrować** przycisk, aby utworzyć rejestrację aplikacji usługi Azure AD.
5. Na wyświetlonej stronie Skopiuj **identyfikator aplikacji (klienta)** . Firma Microsoft będzie odnosił się do tej wartości jako `APPID` w [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) samouczka.

![Zrzut ekranu przedstawiający stronę obiektu aplikacji](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Utwórz klucz tajny klienta

Wygeneruj klucz tajny klienta do uwierzytelniania aplikacji do usługi Azure Active Directory.

1. W **Zarządzaj** sekcji na stronie rejestracji aplikacji kliknij **certyfikaty i klucze tajne**.
2. Na **certyfikaty i klucze tajne** okienku kliknij **+ nowy wpis tajny klienta**.  **Dodać wpis tajny klienta** zostanie wyświetlone okienko.
3. Podaj **opis**.
4. Ustaw **Expires** do czasu trwania wolisz, na przykład **za 2 lata**.
5. Kliknij przycisk **Dodaj** i wartości klucza będzie wyświetlany w **wpisów tajnych klienta** części strony.
6. Skopiuj wartość klucza. Firma Microsoft będzie odnosił się do tej wartości jako `SECRET` w [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) samouczka.
 
![Zrzut ekranu przedstawiający okienko certyfikaty i klucze tajne](./media/howto-create-tenant/create-key.png)
 
Aby uzyskać więcej informacji dotyczących obiektów w aplikacji Azure, zobacz [aplikacji i obiektów nazw głównych usług w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Aby uzyskać szczegółowe informacje na temat tworzenia nowej aplikacji usługi Azure AD, zobacz [rejestrowanie aplikacji z punktem końcowym usługi Azure Active Directory w wersji 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="resources"></a>Zasoby

* [Aplikacje i obiektów nazw głównych usług w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [Szybki start: Rejestrowanie aplikacji z punktem końcowym usługi Azure Active Directory w wersji 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

## <a name="next-steps"></a>Kolejne kroki

Jeśli zostały spełnione wszystkie [wymagania wstępne platformy Azure Red Hat OpenShift](howto-setup-environment.md), wszystko będzie gotowe utworzyć swój pierwszy klaster!

Wypróbuj samouczek:
> [!div class="nextstepaction"]
> [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md)