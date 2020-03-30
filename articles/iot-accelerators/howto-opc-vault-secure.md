---
title: Zabezpiecz komunikację urządzeń OPC UA z OPC Vault - Azure | Dokumenty firmy Microsoft
description: Jak zarejestrować aplikacje OPC UA i jak wystawiać podpisane certyfikaty aplikacji dla urządzeń OPC UA w OPC Vault.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: af5e511cbf273bc4e4fa0a08d089a955426fe75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75454194"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Korzystanie z usługi zarządzania certyfikatami OPC Vault

W tym artykule wyjaśniono, jak zarejestrować aplikacje i jak wystawiać podpisane certyfikaty aplikacji dla urządzeń OPC UA.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="deploy-the-certificate-management-service"></a>Wdrażanie usługi zarządzania certyfikatami

Najpierw wdrożyć usługę do chmury platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Wdrażanie usługi zarządzania certyfikatami OPC Vault](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Tworzenie certyfikatu urzędu certyfikacji wystawcy

Jeśli jeszcze tego nie zrobiono, utwórz certyfikat urzędu certyfikacji wystawcy. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie certyfikatu wystawcy dla programu OPC Vault i zarządzanie nim](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Bezpieczne aplikacje OPC UA

### <a name="step-1-register-your-opc-ua-application"></a>Krok 1: Zarejestruj swoją aplikację OPC UA 

> [!IMPORTANT]
> Rola modułu zapisującego jest wymagana do zarejestrowania aplikacji.

1. Otwórz usługę certyfikatów w `https://myResourceGroup-app.azurewebsites.net`punkcie , i zaloguj się.
2. Przejdź do **rejestru nowego**. W przypadku rejestracji aplikacji użytkownik musi mieć przypisaną co najmniej rolę modułu zapisującego.
2. Formularz wpisu jest zgodny z konwencjami nazewnictwa w UA OPC. Na przykład na poniższym zrzucie ekranu wyświetlane są ustawienia [przykładowego serwera referencyjnego OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) w stosie OPC UA .NET Standard:

   ![Zrzut ekranu przedstawiający rejestrację serwera odwołań UA](media/howto-opc-vault-secure/reference-server-registration.png "Rejestracja serwera odwołań UA")

5. Wybierz **zarejestruj,** aby zarejestrować aplikację w bazie danych aplikacji usługi certyfikatów. Przepływ pracy bezpośrednio prowadzi użytkownika do następnego kroku, aby zażądać podpisanego certyfikatu dla aplikacji.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Krok 2: Zabezpiecz aplikację za pomocą certyfikatu aplikacji podpisanego przez urząd certyfikacji

Zabezpiecz aplikację OPC UA, wystawiając podpisany certyfikat na podstawie żądania podpisania certyfikatu (CSR). Alternatywnie można zażądać nowej pary kluczy, która zawiera nowy klucz prywatny w formacie PFX lub PEM. Aby uzyskać informacje o tym, która metoda jest obsługiwana dla aplikacji, zobacz dokumentację urządzenia OPC UA. Ogólnie rzecz biorąc zaleca się metodę CSR, ponieważ nie wymaga ona przesyłania klucza prywatnego za przewód.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Żądanie nowego certyfikatu z nowym kluczem

1. Przejdź do **aplikacji**.
3. Wybierz **pozycję Nowe żądanie** dla wymienionej aplikacji.

   ![Zrzut ekranu przedstawiający nowy certyfikat żądania](media/howto-opc-vault-secure/request-new-certificate.png "Poproś o nowy certyfikat")

3. Wybierz **pozycję Poproś o nowy klucz KeyPair i Certyfikat,** aby zażądać klucza prywatnego i nowego podpisanego certyfikatu z kluczem publicznym dla aplikacji.

   ![Zrzut ekranu przedstawiający generowanie nowego klucza i certyfikatu](media/howto-opc-vault-secure/generate-new-key-pair.png "Generowanie nowej pary kluczy")

4. Wypełnij formularz tematem i nazwami domen. W przypadku klucza prywatnego wybierz polecenie PEM lub PFX z hasłem. Wybierz **pozycję Generuj nowy klucz keypair,** aby utworzyć żądanie certyfikatu.

   ![Zrzut ekranu przedstawiający szczegóły żądania certyfikatu](media/howto-opc-vault-secure/approve-reject.png "Zatwierdzanie certyfikatu")

5. Zatwierdzenie wymaga użytkownika z rolą osoby zatwierdzającej i uprawnienia do podpisywania w usłudze Azure Key Vault. W typowym przepływie pracy role osoby zatwierdzającej i żądającej powinny być przypisane do różnych użytkowników. Wybierz **opcję Zatwierdź** lub **Odrzuć,** aby rozpocząć lub anulować rzeczywiste utworzenie pary kluczy i operację podpisywania. Nowa para kluczy jest tworzona i bezpiecznie przechowywana w usłudze Azure Key Vault, dopóki nie zostanie pobrana przez żądający certyfikatu. Wynikowy certyfikat z kluczem publicznym jest podpisywane przez urząd certyfikacji. Zakończenie tych operacji może potrwać kilka sekund.

   ![Zrzut ekranu przedstawiający szczegóły żądania certyfikatu z komunikatem o zatwierdzeniu na dole](media/howto-opc-vault-secure/view-key-pair.png "Wyświetl parę klawiszy")

7. Wynikowy klucz prywatny (PFX lub PEM) i certyfikat (DER) można pobrać stąd w formacie wybranym jako binarny plik do pobrania. Dostępna jest również wersja zakodowana w kodowanym base64, na przykład do kopiowania i wklejania certyfikatu do wiersza polecenia lub wpisu tekstowego. 
8. Po bezpiecznym pobraniu i zapisaniu klucza prywatnego można wybrać **opcję Usuń klucz prywatny**. Certyfikat z kluczem publicznym pozostaje dostępny do wykorzystania w przyszłości.
9. Ze względu na użycie certyfikatu podpisanego przez urząd certyfikacji certyfikat urzędu certyfikacji i listy odwołania certyfikatów (CRL) należy również pobrać tutaj.

Teraz to zależy od urządzenia OPC UA, jak zastosować nową parę kluczy. Zazwyczaj certyfikat urzędu certyfikacji i lista CRL `trusted` są kopiowane do folderu, podczas gdy klucze publiczne i prywatne certyfikatu aplikacji są stosowane do `own` folderu w magazynie certyfikatów. Niektóre urządzenia mogą już obsługiwać wypychanie aktualizacji certyfikatów przez serwer. Zapoznaj się z dokumentacją urządzenia OPC UA.

#### <a name="request-a-new-certificate-with-a-csr"></a>Żądanie nowego certyfikatu z csr 

1. Przejdź do **aplikacji**.
3. Wybierz **pozycję Nowe żądanie** dla wymienionej aplikacji.

   ![Zrzut ekranu przedstawiający nowy certyfikat żądania](media/howto-opc-vault-secure/request-new-certificate.png "Poproś o nowy certyfikat")

3. Wybierz **pozycję Poproś o nowy certyfikat z żądaniem podpisania,** aby zażądać nowego podpisanego certyfikatu dla aplikacji.

   ![Zrzut ekranu przedstawiający generowanie nowego certyfikatu](media/howto-opc-vault-secure/generate-new-certificate.png "Generowanie nowego certyfikatu")

4. Przekaż csr, wybierając plik lokalny lub wklejając w formularzu kodowany csr base64. Wybierz **pozycję Generuj nowy certyfikat**.

   ![Zrzut ekranu przedstawiający szczegóły żądania certyfikatu](media/howto-opc-vault-secure/approve-reject-csr.png "Zatwierdzanie csr")

5. Zatwierdzenie wymaga użytkownika z rolą osoby zatwierdzającej i uprawnienia do podpisywania w usłudze Azure Key Vault. Wybierz **pozycję Zatwierdź** lub **Odrzuć,** aby rozpocząć lub anulować rzeczywistą operację podpisywania. Wynikowy certyfikat z kluczem publicznym jest podpisywane przez urząd certyfikacji. Ta operacja może potrwać kilka sekund, aby zakończyć.

   ![Zrzut ekranu przedstawiający szczegóły żądania certyfikatu z komunikatem o zatwierdzeniu na dole](media/howto-opc-vault-secure/view-cert-csr.png "Wyświetl certyfikat")

6. Wynikowy certyfikat (DER) można pobrać z tego miejsca jako plik binarny. Dostępna jest również wersja zakodowana w kodowanym base64, na przykład do kopiowania i wklejania certyfikatu do wiersza polecenia lub wpisu tekstowego. 
10. Po bezpiecznym pobraniu i zapisaniu **certyfikatu**można wybrać opcję Usuń certyfikat .
11. Ze względu na użycie certyfikatu podpisanego przez urząd certyfikacji certyfikat urzędu certyfikacji i listy CRL należy również pobrać tutaj.

Teraz to zależy od urządzenia OPC UA, jak zastosować nowy certyfikat. Zazwyczaj certyfikat urzędu certyfikacji i lista CRL `trusted` są kopiowane do folderu, `own` podczas gdy certyfikat aplikacji jest stosowany do folderu w magazynie certyfikatów. Niektóre urządzenia mogą już obsługiwać wypychanie aktualizacji certyfikatów przez serwer. Zapoznaj się z dokumentacją urządzenia OPC UA.

### <a name="step-3-device-secured"></a>Krok 3: Urządzenie zabezpieczone

Urządzenie OPC UA jest teraz gotowe do komunikowania się z innymi urządzeniami OPC UA zabezpieczonymi certyfikatami urzędu certyfikacji bez dalszej konfiguracji.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy nauczyłeś się zabezpieczać urządzenia OPC UA, możesz:

> [!div class="nextstepaction"]
> [Uruchamianie bezpiecznej usługi zarządzania certyfikatami](howto-opc-vault-secure-ca.md)
