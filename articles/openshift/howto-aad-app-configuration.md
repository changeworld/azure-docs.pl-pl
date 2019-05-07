---
title: Tworzenie usługi Azure AD, rejestracji aplikacji i użytkownika dla Azure Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć jednostkę usługi, wygenerować klienta wpisu tajnego i uwierzytelnianie adresów URL wywołania zwrotnego i utworzenie nowego użytkownika usługi Active Directory do testowania aplikacji w klastrze usługi Microsoft Azure Red Hat OpenShift.
author: tylermsft
ms.author: twhitney
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/06/2019
ms.openlocfilehash: de3f3c30848d26ea399bcccc29a6149a149f6a55
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078523"
---
# <a name="create-an-azure-ad-app-registration-and-user-for-azure-red-hat-openshift"></a>Tworzenie usługi Azure AD, rejestracji aplikacji i użytkownika dla Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift musi mieć uprawnienia do wykonywania zadań w imieniu klastra. Jeśli Twoja organizacja nie ma jeszcze rejestracji aplikacji usługi Azure Active Directory (Azure AD) do użycia jako nazwa główna usługi, wykonaj te instrukcje, aby go utworzyć.

Ten temat zawiera instrukcje dotyczące tworzenia nowego użytkownika usługi Azure AD, który należy do dostępu do aplikacji działających w klastrze Azure Red Hat OpenShift.

Jeśli nie utworzono już dzierżawę usługi Azure AD, postępuj zgodnie z instrukcjami w [utworzyć dzierżawę usługi Azure AD dla usługi Azure Red Hat OpenShift](howto-create-tenant.md) przed wykonaniem tych instrukcji.

## <a name="create-a-new-app-registration"></a>Tworzenie nowej rejestracji aplikacji

Aplikacja, która chce korzystać z możliwości usługi Azure AD, najpierw musi być zarejestrowana w dzierżawę usługi Azure AD. Ten proces rejestracji polega na tym, zapewniając usługi Azure AD szczegóły dotyczące Twojej aplikacji, takich jak adres URL, w którym znajduje się aplikacja, adres URL do wysyłania odpowiedzi po użytkownik jest uwierzytelniany, identyfikator URI, który identyfikuje aplikację i tak dalej.

1. W [witryny Azure portal](https://portal.azure.com), upewnij się, że dzierżawy jest wyświetlany w obszarze swoją nazwę użytkownika w prawym górnym rogu portalu:

    ![Zrzut ekranu przedstawiający portal z dzierżawą wymienione w prawym górnym rogu][tenantcallout] Jeśli nieprawidłowej dzierżawy jest wyświetlany, kliknij swoją nazwę użytkownika w prawym górnym rogu, a następnie kliknij przycisk **Przełącz katalog**i wybierz poprawny dzierżawę z **wszystkie Katalogi** listy.

2. Otwórz [bloku rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) i kliknij przycisk **rejestrowanie nowej aplikacji**.
3. W **Utwórz** okienku, wprowadź przyjazną nazwę dla obiektu aplikacji.
4. Upewnij się, że **typ aplikacji** ustawiono *aplikacji/interfejs API sieci Web*.
5. Tworzenie **adres URL logowania** przy użyciu następującego wzorca:

    ```
    https://<cluster-name>.<azure-region>.cloudapp.azure.com
    ```

    . . . gdzie `<cluster-name>` jest zamierzony nazwą klastra usługi Azure Red Hat OpenShift i `<azure-region>` jest [regionu świadczenia usługi Azure hostowania klastra usługi Azure Red Hat OpenShift](supported-resources.md#azure-regions). Na przykład, jeśli nazwa klastra ma być `contoso`, i zostanie utworzony w `eastus` regionu, w pełni kwalifikowana nazwa domeny (FQDN), musisz wprowadzić dla **adres URL logowania** będzie `https://contoso.eastus.cloudapp.azure.com`

    > [!IMPORTANT]
    > Nazwa klastra musi zawierać tylko małe litery, a nazwa FQDN adresu URL muszą być unikatowe.
    > Upewnij się, że wybrano wystarczająco odrębnej nazwy klastra.

    Zanotuj nazwę klastra i adres URL logowania — będą potrzebne później do dostęp do aplikacji, uruchomione w klastrze. Firma Microsoft będzie odnosił się do nazwy klastra jako `CLUSTER_NAME`, a ten adres URL logowania jako `FQDN` w [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) samouczka.

6. Upewnij się, Twoje **adres URL logowania** wartość weryfikuje z zielony znacznik wyboru. (Naciśnij klawisz Tab, aby skoncentrować się poza *adres URL logowania* pola, a następnie uruchom sprawdzenie poprawności.)
7. Kliknij przycisk **Utwórz** przycisk, aby utworzyć obiekt aplikacji usługi Azure AD.
8. Na **zarejestrowana aplikacja** stronie, skopiuj **identyfikator aplikacji**. Firma Microsoft będzie odnosił się do tej wartości jako `APPID` w [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) samouczka.

    ![Zrzut ekranu przedstawiający w polu tekstowym Identyfikator aplikacji][appidimage]
    
Aby uzyskać więcej informacji dotyczących obiektów w aplikacji Azure, zobacz [aplikacji i obiektów nazw głównych usług w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Aby uzyskać szczegółowe informacje na temat tworzenia nowej aplikacji usługi Azure AD, zobacz [rejestrowanie aplikacji z punktem końcowym usługi Azure Active Directory w wersji 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

### <a name="create-a-client-secret"></a>Utwórz klucz tajny klienta

Teraz możesz przystąpić do generowania klucz tajny klienta do uwierzytelniania aplikacji do usługi Azure Active Directory.

1. Z **zarejestrowana aplikacja** kliknij na **ustawienia** aby otworzyć ustawienia dla zarejestrowanych aplikacji.
2. Na **ustawienia** okienko zostanie wyświetlone, kliknij przycisk **klucze**.  **Klucze** zostanie wyświetlone okienko.
![Zrzut ekranu przedstawiający stronę klucza Utwórz w portalu][createkeyimage]
3. Podaj klucz **opis**.
4. Ustaw wartość **Expires**, na przykład *za 2 lata*.
5. Kliknij przycisk **Zapisz** i pojawi się wartość klucza.
6. Skopiuj wartość klucza. Firma Microsoft będzie odnosił się do tej wartości jako `SECRET` w [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) samouczka.

### <a name="create-a-reply-url"></a>Utwórz adres URL odpowiedzi

Usługa Azure AD używa obiektu aplikacji *adres URL odpowiedzi* do określenia wywołania zwrotnego do użycia podczas autoryzowania aplikacji. W przypadku interfejsu API sieci web lub aplikacji sieci web adres URL odpowiedzi to lokalizacja, w którym usługi Azure AD wysyła odpowiedź uwierzytelniania, w tym token, jeśli uwierzytelnianie zakończyło się pomyślnie.

Nawet najmniejszy niezgodność (końcowe Brak ukośnika, innej wielkości liter) spowoduje niepowodzenie operacji wystawiania tokenów i nie będzie można zalogować.

1. Wróć do **ustawienia** okienko (możesz kliknąć pozycję **ustawienia** w obszarze nawigacji w górnej części portalu) i kliknij przycisk **adresy URL odpowiedzi** po prawej stronie.  **Adresy URL odpowiedzi** zostanie wyświetlone okienko.
2. Pierwszy adres URL odpowiedzi na liście będzie `FQDN` wartość z kroku 6 [tworzenia nowej rejestracji aplikacji](#create-a-new-app-registration). Edytuj go i Dołącz `/oauth2callback/Azure%20AD`.  Na przykład adres URL odpowiedzi powinien teraz wyglądać mniej więcej tak `https://mycluster.eastus.cloudapp.azure.com/oauth2callback/Azure%20AD`
3. Kliknij przycisk **Zapisz** można zapisać adres URL odpowiedzi.

## <a name="create-a-new-active-directory-user"></a>Tworzenie nowego użytkownika usługi Active Directory

Utwórz nowego użytkownika w usłudze Active Directory na potrzeby logowania do aplikacji działającej w klastrze usługi Azure Red Hat OpenShift.

1. Przejdź do [Użytkownicy — Wszyscy użytkownicy](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) bloku.
2. Kliknij przycisk **+ nowy użytkownik**. **Użytkownika** zostanie wyświetlone okienko.
3. Wprowadź **nazwa** Ci jakiejś dla tego użytkownika.
4. Tworzenie **nazwa_użytkownika** na podstawie nazwy dzierżawy, który został utworzony z `.onmicrosoft.com` dołączany na końcu. Na przykład `yourUserName@yourTenantName.onmicrosoft.com`. Zanotuj nazwę tego użytkownika. Będzie potrzebny do logowania się na korzystanie z aplikacji w klastrze.
5. Kliknij przycisk **roli w katalogu** i wybierz **administratora globalnego** a następnie kliknij przycisk **Ok** w dolnej części okienka.
6. W środku elementu **użytkownika** okienku kliknij **Pokaż hasło** i Zapisz hasło tymczasowe. Po zalogowaniu się po raz pierwszy, zostanie wyświetlony monit, aby je zresetować.
7. W dolnej części okienka kliknij **Utwórz** do utworzenia użytkownika.

## <a name="resources"></a>Zasoby

* [Aplikacje i obiektów nazw głównych usług w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [Szybki start: Rejestrowanie aplikacji z punktem końcowym usługi Azure Active Directory w wersji 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

[appidimage]: ./media/howto-create-tenant/get-app-id.png
[createkeyimage]: ./media/howto-create-tenant/create-key.png
[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="next-steps"></a>Kolejne kroki

Jeśli zostały spełnione wszystkie [wymagania wstępne platformy Azure Red Hat OpenShift](howto-setup-environment.md), wszystko będzie gotowe utworzyć swój pierwszy klaster!

Wypróbuj samouczek:
> [!div class="nextstepaction"]
> [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md)
