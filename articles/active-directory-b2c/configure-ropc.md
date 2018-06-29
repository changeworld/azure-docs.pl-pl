---
title: Konfigurowanie przepływu poświadczenia hasła właściciela zasobu w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować przepływ poświadczenia hasła właściciela zasobu w usłudze Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/24/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 073af4a57d55eb8b2f3608482159b57c7b408f3b
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102604"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-ad-b2c"></a>Konfigurowanie przepływu poświadczenia hasła właściciela zasobu w usłudze Azure AD B2C

Przepływu poświadczeń (ROPC) hasło właściciela zasobów jest przepływ standardowego uwierzytelniania OAuth, gdzie aplikacja, znanej także jako uzależnionej wymiany prawidłowe poświadczenia, takie jak nazwa użytkownika i hasło dla tokenu identyfikator, token dostępu i token odświeżania. 

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.

W usłudze Azure Active Directory (Azure AD) B2C obsługiwane są następujące opcje:

- **Aplikacja Native Client**: interakcji użytkownika podczas uwierzytelniania się stanie, jeśli kod jest uruchamiany na urządzeniu po stronie użytkownika. Urządzenie może zostać przenośnych aplikacja, która jest uruchomiona w macierzystym systemu operacyjnego, takich jak Android, działającego w przeglądarce, takich jak JavaScript.
- **Przepływ publicznego klienta**: tylko poświadczenia użytkownika, zebranych przez aplikację, są wysyłane w wywołaniu interfejsu API. Poświadczenia aplikacji nie są wysyłane.
- **Dodawanie nowych oświadczeń**: identyfikator tokenu zawartość do dodawania nowych oświadczeń, można zmienić. 

Nie są obsługiwane następujące przepływów:

- **Serwer serwer**: system ochrony tożsamości potrzebuje niezawodnej adres IP gromadzone wywołujący (klientami) w ramach interakcji. W wywołaniu interfejsu API po stronie serwera jest używany tylko adres IP serwera. Po przekroczeniu progu dynamiczne uwierzytelnianie nie powiodło się, system ochrony tożsamości mogą identyfikować powtarzane adresu IP jako osoba atakująca.
- **Przepływ poufne klienta**: identyfikator klienta aplikacji jest weryfikowane, ale nie jest sprawdzana poprawność klucz tajny aplikacji.

##  <a name="create-a-resource-owner-policy"></a>Utwórz zasadę właściciel zasobu

1. Zaloguj się do portalu Azure jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Aby przełączyć się do dzierżawy usługi Azure AD B2C, wybierz katalog usługi B2C w prawym górnym rogu portalu.
3. W obszarze **zasady**, wybierz pozycję **zasady właściciel zasobu**.
4. Podaj nazwę zasad, takich jak *ROPC_Auth*, a następnie wybierz **oświadczenia aplikacji**.
5. Wybrać oświadczenia aplikacji, wymagane dla aplikacji, takich jak *Nazwa wyświetlana*, *adres E-mail*, i *dostawcy tożsamości*.
6. Wybierz przycisk **OK**, a następnie wybierz pozycję **Utwórz**.

   Zostanie wyświetlony punkt końcowy, np. w tym przykładzie:

   `https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>Rejestrowanie aplikacji

1. W ustawieniach B2C wybierz **aplikacji**, a następnie wybierz **Dodaj**.
2. Wprowadź nazwę aplikacji, takich jak *ROPC_Auth_app*.
3. Wybierz **nr** dla **interfejsu API sieci Web i aplikacji sieci Web**, a następnie wybierz **tak** dla **Native client**.
4. Pozostaw wszystkie inne wartości są, a następnie wybierz **Utwórz**.
5. Wybierz nową aplikację i Zanotuj identyfikator aplikacji do późniejszego użycia.

## <a name="test-the-policy"></a>Testowanie zasad

Generuj wywołania interfejsu API za pomocą ulubionych aplikacji programowanie interfejsu API i przejrzyj odpowiedzi do debugowania zasady. Utworzyć wywołania takie informacje w poniższej tabeli jako treści żądania POST:
- Zastąp  *\<yourtenant.onmicrosoft.com >* o nazwie dzierżawy usługi B2C.
- Zastąp  *\<B2C_1A_ROPC_Auth >* o pełnej nazwie zasadami poświadczenia hasła właściciela zasobu.
- Zastąp  *\<bef2222d56-552f-4a5b-b90a-1988a7d634c3 >* wraz z Identyfikatorem aplikacji z rejestracji.

`https://login.microsoftonline.com/<yourtenant.onmicrosoft.com>/<B2C_1A_ROPC_Auth>/oauth2/v2.0/token`

| Klucz | Wartość |
| --- | ----- |
| nazwa użytkownika | leadiocl@outlook.com |
| hasło | Passxword1 |
| Typ grant_type | hasło |
| scope | openid \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > offline_access |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| response_type | Token żądaniu |

*Client_id* jest wartością zanotowaną wcześniej jako identyfikator aplikacji. *Offline_access* jest opcjonalne, jeśli chcesz otrzymywać token odświeżania. 

Rzeczywiste żądania POST wygląda następująco:

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Odpowiedź oznaczająca Powodzenie dostęp w trybie offline wygląda następująco:

```
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>Realizowanie token odświeżania

Utworzyć wywołanie metody POST, tak jak pokazano tutaj informacje w poniższej tabeli jako treść żądania:

`https://login.microsoftonline.com/<yourtenant.onmicrosoft.com>/<B2C_1A_ROPC_Auth>/oauth2/v2.0/token`

| Klucz | Wartość |
| --- | ----- |
| Typ grant_type | refresh_token |
| response_type | żądaniu |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| zasób | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* i *zasobów* wartości zanotowanych wcześniej jako identyfikator aplikacji. *Refresh_token* jest tokenem otrzymany w już wspomniano połączenia uwierzytelniającego.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>Wdrożenia z preferowanych natywnego zestawu SDK lub Użyj uwierzytelniania aplikacji

Wdrożenia usługi Azure AD B2C spełnia standardy protokołu OAuth 2.0 na potrzeby poświadczenia hasła właściciela zasobu publicznego klienta oraz powinien być zgodny z klientem większość zestawów SDK. Przetestowano tego przepływu w szerokim zakresie, w środowisku produkcyjnym z AppAuth dla systemu iOS i AppAuth dla systemu Android. Aby uzyskać najnowsze informacje, zobacz [natywny zestaw SDK aplikacji OAuth 2.0 i OpenID Connect implementacja nowoczesnych najlepsze rozwiązania](https://appauth.io/).

Pobierz przykłady pracy, które zostały skonfigurowane do użycia z usługą Azure AD B2C z serwisu GitHub, [dla systemu Android](https://aka.ms/aadb2cappauthropc) i [dla systemu iOS](https://aka.ms/aadb2ciosappauthropc).




