Jeśli chcesz włączyć tylko rejestracji w swojej aplikacji, możesz użyć **rejestracji** zasad. Te zasady opisują procesy, które klienci przechodzą przez podczas tworzenia konta i zawartość tokenów, które aplikacja otrzyma po pomyślnej rejestracji.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Kliknij przycisk **Zasady tworzenia konta**.

Kliknij pozycję **+Dodaj** w górnej części bloku.

**Nazwa** określa nazwę zasad rejestrowania używanych przez aplikację. Na przykład wprowadź wartość **SiUp**.

Kliknij pozycję **Dostawcy tożsamości** i wybierz pozycję **Tworzenie konta za pomocą adresu e-mail**. Opcjonalnie możesz również wybrać dostawców tożsamości społecznościowych, jeśli są już skonfigurowani. Kliknij przycisk **OK**.

Kliknij pozycję **Atrybuty tworzenia konta**. W tym miejscu można wybrać atrybuty, które mają być zbierane od użytkownika podczas rejestracji. Na przykład wybierz pozycje **Kraj/region**, **Nazwa wyświetlana** i **Kod pocztowy**. Kliknij przycisk **OK**.

Wybierz pozycję **Oświadczenia aplikacji**. Tutaj wybierasz oświadczenia, które mają być zwracane w tokenach wysyłanych z powrotem do aplikacji po pomyślnej rejestracji. Na przykład wybierz pozycje **Nazwa wyświetlana**, **Dostawca tożsamości**, **Kod pocztowy**, **Użytkownik jest nowy** i **Identyfikator obiektu użytkownika**.

Kliknij przycisk **Utwórz**. Utworzone zasady są wyświetlane jako **B2C_1_SiUp** (fragment **B2C\_1\_**  jest dodawany automatycznie) w bloku **Zasady tworzenia konta**.

Otwórz zasady, klikając pozycję **B2C_1_SiUp**.

Wybierz pozycję **Aplikacja Contoso B2C** na liście rozwijanej **Aplikacje** i pozycję `https://localhost:44321/` na liście rozwijanej **Adres URL odpowiedzi/identyfikator URI przekierowania**.

Kliknij pozycję **Uruchom teraz**. Zostanie otwarta nowa karta przeglądarki i możesz zapoznać się z procesem rejestracji w Twojej aplikacji przez użytkownika.

> [!NOTE]
> Utworzenie zasad i zastosowanie aktualizacji zajmuje do minuty.
>