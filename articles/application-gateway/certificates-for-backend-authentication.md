---
title: Certyfikatami wymaganymi dla zaplecza umieszczania na białej liście w usłudze Azure Application Gateway
description: Ten artykuł zawiera przykłady sposobu certyfikat SSL można przekonwertować certyfikatu uwierzytelniania i zaufany certyfikat główny, które są wymagane dla wystąpień zaplecza umieszczania na białej liście w usłudze Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/14/2019
ms.author: absha
ms.openlocfilehash: 72ee9123ad959c0c7240d4f7a906adc1a4dd1a93
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831728"
---
# <a name="create-certificates-for-whitelisting-backend-with-azure-application-gateway"></a>Tworzenie certyfikatów umieszczania na białej liście zaplecza za pomocą usługi Azure Application Gateway

Aby wykonać kompleksowej usługi SSL, usługa application gateway wymaga wystąpieniami zaplecza, do listy dozwolonych, przekazując uwierzytelniania/zaufanych certyfikatów głównych. W przypadku jednostek SKU w wersji 1 do umieszczenia certyfikatów na liście dozwolonych wymagane są certyfikaty uwierzytelniania, natomiast w przypadku jednostek SKU w wersji 2 wymagane są zaufane certyfikaty główne

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> - Eksportowanie certyfikatu uwierzytelniania z certyfikatu wewnętrznej bazy danych (w przypadku jednostek SKU v1)
> - Eksportowanie certyfikatu zaufanego głównego z certyfikatu wewnętrznej bazy danych (w przypadku jednostek SKU v2)

## <a name="prerequisites"></a>Wymagania wstępne

Możesz wymagać od istniejącego certyfikatu wewnętrznej bazy danych do wygenerowania certyfikatów uwierzytelniania lub zaufanych certyfikatów głównych wymaganych do umieszczania na białej liście wewnętrznej bazy danych wystąpień z usługą application gateway. Certyfikatu z wewnętrznej bazy danych może być taka sama jak certyfikat protokołu SSL lub innej w celu zwiększenia poziomu bezpieczeństwa. Usługa Application gateway nie zapewnia żadnych mechanizm do tworzenia lub zakup certyfikatu SSL. Do celów testowych można utworzyć certyfikatu z podpisem własnym, ale nie należy go używać w przypadku obciążeń produkcyjnych. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Eksportowanie certyfikatu uwierzytelniania (w przypadku jednostek SKU v1)

Certyfikat uwierzytelniania jest wymagane do listy dozwolonych wystąpień wewnętrznej bazy danych w wersji 1 z bramy aplikacji jednostki SKU. Certyfikat uwierzytelniania jest certyfikat x.509 szyfrowany algorytmem klucz publiczny certyfikatów serwera zaplecza w Base-64 (. Format CER). W tym przykładzie firma Microsoft używanie certyfikatu protokołu SSL dla certyfikatu wewnętrznej bazy danych i wyeksportować jego klucz publiczny ma być używany jako certyfikacji uwierzytelniania. Ponadto w tym przykładzie użyjemy narzędzia Windows Menedżer certyfikatów można wyeksportować wymaganych certyfikatów. Można użyć innego narzędzia zgodnie z Twojej wygody.

Od certyfikatu protokołu SSL należy wyeksportować plik cer klucza publicznego (nie klucz prywatny). Następujące kroki pomocy, możesz wyeksportować cer certyfikat x.509 szyfrowany algorytmem Base-64 pliku (. Format CER) dla certyfikatu:

1. Aby uzyskać plik cer z certyfikatu, otwórz okno **Zarządzaj certyfikatami użytkowników**. Znajdź certyfikat, zwykle znajduje się w "Certyfikaty - bieżący użytkownik\osobisty\certyfikat", a następnie kliknij prawym przyciskiem myszy. Kliknij pozycję **Wszystkie zadania**, a następnie kliknij pozycję **Eksportuj**. Spowoduje to otwarcie **Kreatora eksportu certyfikatów**. Jeśli nie możesz znaleźć certyfikat w obszarze bieżący użytkownik\osobisty\certyfikat, być może przypadkowo otwarto "Certyfikatów — lokalny komputer", a nie "Certyfikaty — bieżący użytkownik"). Jeśli chcesz otworzyć Menedżera certyfikatów w bieżącym zakresie użytkownika przy użyciu programu PowerShell, możesz wpisać *certmgr* w oknie konsoli.

   ![Eksportowanie](./media/certificates-for-backend-authentication/export.png)

2. W Kreatorze kliknij pozycję **Dalej**.

   ![Eksportowanie certyfikatu](./media/certificates-for-backend-authentication/exportwizard.png)

3. Wybierz pozycję **Nie eksportuj klucza prywatnego**, a następnie kliknij pozycję **Dalej**.

   ![Nie eksportuj klucza prywatnego](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na stronie **Format pliku eksportu** wybierz pozycję **Certyfikat X.509 szyfrowany algorytmem Base-64 (.CER)** , a następnie kliknij pozycję **Dalej**.

   ![Kodowanie Base-64](./media/certificates-for-backend-authentication/base64.png)

5. Aby uzyskać **Eksport pliku**, **Przeglądaj** do lokalizacji, do którego chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **Dalej**.

   ![Browse](./media/certificates-for-backend-authentication/browse.png)

6. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

   ![Zakończ](./media/certificates-for-backend-authentication/finish.png)

7. Twój certyfikat jest został pomyślnie wyeksportowany.

   ![Powodzenie](./media/certificates-for-backend-authentication/success.png)

   Wyeksportowany certyfikat wygląda podobnie do poniższego:

   ![Wyeksportowane](./media/certificates-for-backend-authentication/exported.png)

8. Jeśli otworzysz wyeksportowany certyfikat za pomocą Notatnika, zostanie wyświetlony podobny do tego przykładu. Sekcja w kolorze niebieskim zawiera informacje, które zostanie przekazany do usługi application gateway. Jeśli Otwórz swój certyfikat przy użyciu programu Notepad, a nie wygląda podobnie do poniższego, zwykle oznacza, że nie wyeksportowano go przy użyciu Base-64 certyfikat x.509 szyfrowany algorytmem (. Format CER). Ponadto jeśli chcesz użyć w innym edytorze tekstu, Dowiedz się, że niektóre edytory może prowadzić do niezamierzonych formatowania w tle. To jest utworzenie problemy podczas przekazywania tekst z tego certyfikatu do platformy Azure.

   ![Otwórz w Notatniku](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Wyeksportuj certyfikat zaufany główny urząd certyfikacji (dla jednostki SKU w wersji 2)

Zaufany certyfikat główny jest wymagany do listy dozwolonych wystąpień wewnętrznej bazy danych w usłudze application gateway v2 jednostki SKU. Certyfikat główny, to certyfikat x.509 szyfrowany algorytmem Base-64 (. Certyfikat główny na format CER) z certyfikatów serwera zaplecza. W tym przykładzie firma Microsoft używanie certyfikatu protokołu SSL dla certyfikatu wewnętrznej bazy danych, eksportowania klucza publicznego i wyeksportuj certyfikat główny zaufany urząd certyfikacji z klucza publicznego w formacie zakodowane w formacie base64, aby pobrać certyfikat zaufany główny urząd certyfikacji. 

Poniższe kroki pomogą Ci wyeksportować plik cer dla certyfikatu:

1. Wykonaj kroki opisane w sekcji 1-9 **eksportu certyfikatu uwierzytelniania z certyfikatu wewnętrznej bazy danych (w przypadku jednostek SKU v1)** powyżej, aby wyeksportować klucza publicznego z certyfikatu z wewnętrznej bazy danych.

2. Po został wyeksportowany klucz publiczny, otwórz plik.

   ![Certyfikat Otwórz autoryzacji](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![o certyfikacie](./media/certificates-for-backend-authentication/general.png)

3. Przenieś do ścieżki certyfikacji widokami urzędu certyfikacji.

   ![Szczegóły certyfikatu](./media/certificates-for-backend-authentication/certdetails.png)

4. Wybierz certyfikat główny, a następnie kliknij przycisk na **Wyświetl certyfikat**.

   ![Ścieżka certyfikatu](./media/certificates-for-backend-authentication/rootcert.png)

   Można wyświetlać szczegóły certyfikatu głównego.

   ![informacje o certyfikatu](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Przenieś do **szczegóły** wyświetlić, a następnie kliknij przycisk **Kopiuj do pliku...**

   ![Kopia certyfikatu głównego](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. W tym momencie zostały wyodrębnione szczegóły certyfikatu głównego z certyfikatu z wewnętrznej bazy danych. Zostanie wyświetlony **Kreatora eksportu certyfikatów**. Teraz Użyj kroki 2 – 9, opisanego w sekcji **eksportu certyfikatu uwierzytelniania z certyfikatu wewnętrznej bazy danych (w przypadku jednostek SKU v1)** powyżej można wyeksportować zaufanych głównych certyfikatów w Base-64 certyfikat x.509 szyfrowany algorytmem (. Format CER).

## <a name="next-steps"></a>Kolejne kroki

Teraz masz uwierzytelniania, certyfikat/zaufany certyfikat x.509 szyfrowany algorytmem certyfikatu głównego w Base-64 (. Format CER). Możesz dodać to do usługi application gateway do listy dozwolonych serwerów wewnętrznej bazy danych dla typu end to end szyfrowania SSL. Zobacz [jak skonfigurować kompleksowe szyfrowanie SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).