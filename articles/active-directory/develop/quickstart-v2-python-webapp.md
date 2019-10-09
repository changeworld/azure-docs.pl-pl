---
title: Aplikacja internetowa platformy Microsoft Identity w języku Python — Szybki Start | Azure
description: Dowiedz się, jak zaimplementować logowanie firmy Microsoft w aplikacji sieci Web w języku Python za pomocą OAuth2
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
editor: ''
ms.assetid: 9551f0b5-04f2-44d7-87b5-756409180fe9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 85a1de992be7b5bbdcec8fd415f60ae10190c11a
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170042"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji sieci Web w języku Python

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

W tym przewodniku szybki start dowiesz się, jak zintegrować aplikację sieci Web w języku Python z platformą tożsamości firmy Microsoft. Twoja aplikacja zaloguje użytkownika, uzyskaj token dostępu, aby wywołać interfejs API Microsoft Graph i przetworzyć żądanie do interfejsu API Microsoft Graph.

Po ukończeniu tego przewodnika aplikacja będzie akceptować logowania do osobistych kont Microsoft (w tym outlook.com, live.com i innych) oraz kont służbowych z dowolnej firmy lub organizacji korzystającej z Azure Active Directory.

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Wymagania wstępne

Do uruchomienia tego przykładu potrzebne są:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) lub [Python 3 +](https://www.python.org/downloads/release/python-364/)
- [Kolba](http://flask.pocoo.org/), [podsesja](https://pythonhosted.org/Flask-Session/), [żądania](https://requests.kennethreitz.org//en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) 
- Dzierżawa usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji na temat uzyskiwania dzierżawy usługi Azure AD, zobacz [jak uzyskać dzierżawę usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji szybkiego startu
>
> Dostępne są dwie opcje uruchomienia aplikacji szybkiego startu: Express (opcja 1) i ręczna (opcja 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1: Zarejestruj i automatycznie Skonfiguruj aplikację, a następnie Pobierz przykład kodu
>
> 1. Przejdź do [Rejestracje aplikacji Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2: Zarejestruj i ręcznie skonfiguruj aplikację oraz przykład kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. rejestrowanie aplikacji
>
> Aby zarejestrować aplikację i ręcznie dodać informacje o rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
> 1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu na żądaną dzierżawę usługi Azure AD.
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Gdy zostanie wyświetlona strona **zarejestruj aplikację** , wprowadź informacje o rejestracji aplikacji:
>      - W sekcji **Nazwa** wprowadź zrozumiałą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `python-webapp`.
>      - W obszarze **obsługiwane typy kont**wybierz pozycję **konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft**.
>      - W sekcji **Identyfikator URI przekierowania** , na liście rozwijanej Wybierz platformę **sieci Web** , a następnie ustaw wartość na `http://localhost:5000/getAToken`.
>      - Wybierz pozycję **zarejestruj**. Na stronie **Przegląd** aplikacji Zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
> 1. W menu po lewej stronie wybierz pozycję **certyfikaty & wpisy tajne** i kliknij pozycję **Nowy wpis tajny klienta** w sekcji wpisy **tajne klienta** :
>
>      - Wpisz opis klucza (dla wpisu tajnego aplikacji wystąpienia).
>      - Wybierz kluczowy czas trwania wynoszący **1 rok**.
>      - Po kliknięciu przycisku **Dodaj**zostanie wyświetlona wartość klucza.
>      - Skopiuj wartość klucza. Będzie on potrzebny później.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w Azure Portal
>
> Aby uzyskać przykładowy kod dla tego przewodnika Szybki Start, należy wykonać następujące czynności:
>
> 1. Dodaj adres URL odpowiedzi jako `http://localhost:5000/getAToken`.
> 1. Utwórz klucz tajny klienta.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę dla mnie]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already skonfigurowany @ no__t-1 aplikacja jest skonfigurowana z tym atrybutem

#### <a name="step-2-download-your-project"></a>Krok 2. Pobieranie projektu

[Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>Krok 3. Konfigurowanie aplikacji

1. Wyodrębnij plik zip do folderu lokalnego bliżej folderu głównego — na przykład **C:\Azure-Samples**
1. Jeśli używasz zintegrowanego środowiska programistycznego, Otwórz przykład w ulubionym środowisku IDE (opcjonalnie).
1. Otwórz plik **app_config. PR** , który znajduje się w folderze głównym i Zamień na następujący fragment kodu:

```python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
```

> [!div renderon="docs"]
> Gdzie:
>
> - `Enter_the_Application_Id_here` — to identyfikator aplikacji dla zarejestrowanej aplikacji.
> - `Enter_the_Client_Secret_Here` — jest **kluczem tajnym klienta** utworzonym w **certyfikatach & wpisy tajne** dla zarejestrowanej aplikacji.

#### <a name="step-4-run-the-code-sample"></a>Krok 4. Uruchamianie przykładu kodu

1. Konieczne będzie zainstalowanie biblioteki języka Python MSAL, struktury kolby, sesji do zarządzania sesjami po stronie serwera i żądań przy użyciu funkcji PIP w następujący sposób:

   ```Shell
   pip install -r requirements.txt
   ```

2. Uruchom app.py z powłoki lub wiersza polecenia:

   ```Shell
   python app.py
   ```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat aplikacji sieci Web, które logują użytkowników, a następnie wywołujących interfejsy API sieci Web:

> [!div class="nextstepaction"]
> [Scenariusz: aplikacje sieci Web, które logują użytkowników](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
