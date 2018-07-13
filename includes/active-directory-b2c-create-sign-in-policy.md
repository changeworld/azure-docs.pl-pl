Jeśli chcesz tylko Włącz logowanie w swojej aplikacji, możesz użyć **logowania** zasad. Te zasady opisują procesy, które klienci uzyskują podczas logowania i zawartość tokenów, które aplikacja otrzyma w pomyślnych logowaniach.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
Kliknij przycisk **zasady logowania**.

Kliknij pozycję **+Dodaj** w górnej części bloku.

**Nazwa** określa nazwę zasad logowania używanych przez aplikację. Na przykład wprowadź wartość **SiIn**.

Kliknij pozycję **Dostawcy tożsamości** i wybierz pozycję **Logowanie za pomocą konta lokalnego**. Opcjonalnie możesz również wybrać dostawców tożsamości społecznościowych, jeśli są już skonfigurowani. Kliknij przycisk **OK**.

Wybierz pozycję **Oświadczenia aplikacji**. Tutaj wybierasz oświadczenia, które mają być zwracane w tokenach wysyłanych z powrotem do aplikacji po pomyślnym logowaniu. Na przykład wybierz pozycje **Nazwa wyświetlana**, **Dostawca tożsamości**, **Kod pocztowy** i **Identyfikator obiektu użytkownika**. Kliknij przycisk **OK**.

Kliknij przycisk **Utwórz**. Zauważ, że właśnie utworzone zasady są wyświetlane jako **B2C_1_SiIn** (fragment **B2C\_1\_**  jest dodawany automatycznie) w bloku **Zasady logowania**.

Otwórz zasady, klikając pozycję **B2C_1_SiIn**.

Wybierz pozycję **Aplikacja Contoso B2C** na liście rozwijanej **Aplikacje** i pozycję `https://localhost:44321/` na liście rozwijanej **Adres URL odpowiedzi/identyfikator URI przekierowania**.

Kliknij pozycję **Uruchom teraz**. Zostanie otwarta nowa karta przeglądarki i możesz zapoznać się z procesem logowania się użytkownika do Twojej aplikacji.

> [!NOTE]
> Utworzenie zasad i zastosowanie aktualizacji zajmuje do minuty.
>