Jeśli chcesz umożliwić edytowanie profilu dla aplikacji, użyj **edycji profilu** zasad. Ta zasada opisano procesy, które podczas edycji profilu i zawartość aplikacji zostanie wyświetlony po pomyślnym ukończeniu tokeny przeszli przez klientów.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

W sekcji ustawień dotyczących zasad wybierz pozycję **Zasady edytowania profilu** i kliknij pozycję **+ Dodaj**.

![Wybierz pozycję Zasady edytowania profilu, a następnie kliknij przycisk Dodaj](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-policy.png)

Wprowadź **Nazwę** zasad, do której będzie odwoływać się Twoja aplikacja. Na przykład wprowadź wartość `SiPe`.

Wybierz pozycję **Dostawcy tożsamości** i zaznacz pozycję **Logowanie za pomocą konta lokalnego**. Opcjonalnie możesz również wybrać dostawców tożsamości społecznościowych, jeśli są już skonfigurowani. Kliknij przycisk **OK**.

![Wybierz pozycję Logowanie za pomocą konta lokalnego jako dostawcę tożsamości, a następnie kliknij przycisk OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-identity-providers.png)

Wybierz pozycję **Atrybuty profilu**. Wybierz atrybuty, które użytkownik może wyświetlać i edytować w swoim profilu. Na przykład zaznacz pozycje **Kraj/Region**, **Nazwa wyświetlana** i **Kod pocztowy**. Kliknij przycisk **OK**.

![Wybierz część atrybutów, a następnie kliknij przycisk OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-attributes.png)

Wybierz pozycję **Oświadczenia aplikacji**. Wybierz oświadczenia, które mają być zwracane w tokenach autoryzacji wysyłanych z powrotem do aplikacji po pomyślnym edytowaniu profilu. Na przykład wybierz pozycje **Nazwa wyświetlana**, **Kod pocztowy**.

![Wybierz część oświadczeń aplikacji i kliknij przycisk OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-application-claims.png)

Kliknij przycisk **Utwórz**, aby dodać zasady. Zasady są wyświetlane jako **B2C_1_SiPe**. Do nazwy jest dołączany prefiks **B2C_1_**.

Otwórz zasady, wybierając pozycję **B2C_1_SiPe**. Sprawdź ustawienia określone w tabeli, a następnie kliknij pozycję **Uruchom teraz**.

![Wybierz zasady, a następnie uruchom je](media/active-directory-b2c-create-profile-editing-policy/run-b2c-editing-policy.png)

| Ustawienie      | Wartość  |
| ------------ | ------ |
| **Aplikacje** | Aplikacja Contoso B2C |
| **Wybierz adres URL odpowiedzi** | `https://localhost:44316/` |

Zostanie otwarta nowa karta przeglądarki, na której możesz sprawdzić, czy edytowanie profilu użytkownika działa tak, jak zostało skonfigurowane.

> [!NOTE]
> Utworzenie zasad i zastosowanie aktualizacji zajmuje do minuty.
>