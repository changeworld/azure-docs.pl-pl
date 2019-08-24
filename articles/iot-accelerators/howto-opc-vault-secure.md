---
title: Zabezpieczanie komunikacji urządzeń OPC UA z magazynem OPC — Azure | Microsoft Docs
description: Jak zarejestrować aplikacje OPC UA i jak wystawiać podpisane certyfikaty aplikacji dla urządzeń OPC UA z magazynem OPC.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae10bb3488c21b6637163e333231e95a18c652bf
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996115"
---
# <a name="how-to-use-the-opc-vault-certificate-management-service"></a>Jak używać usługi zarządzania certyfikatami magazynu OPC

W tym artykule wyjaśniono, jak zarejestrować aplikacje i jak wystawiać podpisane certyfikaty aplikacji dla urządzeń z OPC UA.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="deploy-the-certificate-management-service"></a>Wdrażanie usługi zarządzania certyfikatami

Najpierw należy wdrożyć usługę w chmurze platformy Azure.
Zapoznaj się z artykułem opisującym [wdrażanie usługi zarządzania certyfikatami magazynu OPC](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Tworzenie certyfikatu urzędu certyfikacji wystawcy

Jeśli jeszcze tego nie zrobiono, Utwórz certyfikat urzędu certyfikacji wystawcy.

Znajdź artykuł opisujący sposób [tworzenia certyfikatu wystawcy dla magazynu OPC i zarządzania](howto-opc-vault-manage.md)nim.

## <a name="secure-opc-ua-applications"></a>Bezpieczne aplikacje OPC UA

### <a name="step-1-register-your-opc-ua-application"></a>Krok 1: Rejestrowanie aplikacji OPC UA 

> [!IMPORTANT]
> Rola "Writer" jest wymagana do zarejestrowania aplikacji.

1. Otwórz usługę certyfikatów pod adresem `https://myResourceGroup-app.azurewebsites.net` i zaloguj się.
2. Przejdź do `Register New` strony.
1. W przypadku rejestracji aplikacji użytkownik musi mieć co najmniej przypisaną rolę składnika zapisywania.
2. Formularz wprowadzania jest zgodny z konwencjami nazewnictwa na świecie OPC UA. Przykładowo na ilustracji poniżej ustawień przykładowego [serwera referencyjnego OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) w OPC UA. Jest pokazywany standardowy stos:

   ![Rejestracja serwera odwołań UA](media/howto-opc-vault-secure/reference-server-registration.png "Rejestracja serwera odwołań UA")

5. Naciśnij przycisk `Register` , aby zarejestrować aplikację w bazie danych aplikacji usługi certyfikatów. Przepływ pracy bezpośrednio prowadzi użytkownika do następnego kroku, aby zażądać certyfikatu podpisanego dla aplikacji.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Krok 2: Zabezpieczanie aplikacji przy użyciu certyfikatu podpisanej aplikacji urzędu certyfikacji

Aplikację OPC UA można zabezpieczyć, wydając podpisany certyfikat na podstawie żądania podpisania certyfikatu (CSR) lub żądając nowej pary kluczy, która zawiera również nowy klucz prywatny w formacie PFX lub PEM. Postępuj zgodnie z dokumentacją urządzenia OPC UA, na którym metoda jest obsługiwana dla Twojej aplikacji. Ogólnie rzecz biorąc, zalecane jest użycie metody CSR, ponieważ nie wymaga ona transferu klucza prywatnego za pośrednictwem sieci przewodowej.

- Jeśli urządzenie wymaga wystawienia nowej pary kluczy, należy postępować zgodnie z [Step3a](##step-3a-request-a-new-certificate-with-a-new-keypair).
- Jeśli urządzenie obsługuje wystawianie żądania podpisania certyfikatu (CSR), należy postępować zgodnie z [Step3b](#step-3b-request-a-new-certificate-with-a-csr).

### <a name="step-3a-request-a-new-certificate-with-a-new-keypair"></a>Krok 3a: Zażądaj nowego certyfikatu przy użyciu nowej pary kluczy

1. Przejdź do `Applications` strony.
3. Uruchom przepływ pracy żądania certyfikatu, wybierając `New Request` aplikację z listy.

   ![Żądaj nowego certyfikatu](media/howto-opc-vault-secure/request-new-certificate.png "Żądaj nowego certyfikatu")

3. Naciśnij pozycję "Żądaj nowej pary kluczy i certyfikatu", aby zażądać klucza prywatnego i nowego podpisanego certyfikatu z kluczem publicznym aplikacji.

   ![Generuj nową parę kluczy](media/howto-opc-vault-secure/generate-new-key-pair.png "Generuj nową parę kluczy")

4. Wypełnij formularz temat, nazwy domen i wybierz PEM lub PFX z hasłem klucza prywatnego. Naciśnij przycisk `Generate New Certificate` , aby utworzyć żądanie certyfikatu.

   ![Zatwierdź certyfikat](media/howto-opc-vault-secure/approve-reject.png "Zatwierdź certyfikat")

5. Krok zatwierdzania wymaga użytkownika z rolą "osoba zatwierdzająca" i uprawnieniami do podpisywania w programie Azure Key Vault. W typowym przepływie pracy osoby zatwierdzającej i roli żądającej powinny być przypisane do różnych użytkowników.
6. Zatwierdź lub Odrzuć żądanie certyfikatu, aby uruchomić lub anulować rzeczywiste Tworzenie pary kluczy i operacji podpisywania. Nowa para kluczy jest tworzona i bezpiecznie przechowywana w Azure Key Vault do momentu pobrania przez zleceniodawcę certyfikatu. Certyfikat wynikający z kluczem publicznym jest podpisany przez urząd certyfikacji. Wykonywanie tych operacji może potrwać kilka sekund.

   ![Wyświetl parę kluczy](media/howto-opc-vault-secure/view-key-pair.png "Wyświetl parę kluczy")

7. Klucz prywatny (PFX lub PEM) i certyfikat (DER) można pobrać z tego miejsca w formacie wybranym jako plik binarny do pobrania. Dostępna jest również wersja zakodowana algorytmem Base64, na przykład w celu kopiowania wklejenia certyfikatu do wiersza polecenia lub wprowadzania tekstu. 
8. Po pobraniu i zapisaniu klucza prywatnego można go usunąć z usługi za pomocą `Delete Private Key` przycisku. Certyfikat z kluczem publicznym pozostaje dostępny do użytku w przyszłości.
9. Ze względu na użycie certyfikatu podpisanego przez urząd certyfikacji należy również pobrać certyfikat urzędu certyfikacji i listę CRL.
10. Teraz zależy to od urządzenia OPC UA, jak zastosować nową parę kluczy. Zazwyczaj certyfikat urzędu certyfikacji i listy CRL są kopiowane do `trusted` folderu, podczas gdy publiczny i prywatny klucz certyfikatu aplikacji jest stosowany `own` do folderu w magazynie certyfikatów. Niektóre urządzenia mogą już obsługiwać "wypychanie" serwera dla aktualizacji certyfikatów. Zapoznaj się z dokumentacją urządzenia OPC UA.

### <a name="step-3b-request-a-new-certificate-with-a-csr"></a>Krok 3B: Zażądaj nowego certyfikatu za pomocą CSR 

1. Przejdź do `Applications` strony.
3. Uruchom przepływ pracy żądania certyfikatu, wybierając `New Request` aplikację z listy.

   ![Żądaj nowego certyfikatu](media/howto-opc-vault-secure/request-new-certificate.png "Żądaj nowego certyfikatu")

3. Naciśnij pozycję "Żądaj nowego certyfikatu z żądaniem podpisania", aby zażądać nowego podpisanego certyfikatu dla aplikacji.

   ![Generuj nowy certyfikat](media/howto-opc-vault-secure/generate-new-certificate.png "Generuj nowy certyfikat")

4. Przekaż CSR, wybierając plik lokalny lub wklejając w postaci zakodowany w formacie base64 CSR. Naciśnij przycisk `Generate New Certificate` , aby utworzyć żądanie certyfikatu.

   ![Zatwierdź CSR](media/howto-opc-vault-secure/approve-reject-csr.png "Zatwierdź CSR")

5. Krok zatwierdzania wymaga użytkownika z rolą "osoba zatwierdzająca" i uprawnieniami do podpisywania w programie Azure Key Vault. Zatwierdź lub Odrzuć żądanie certyfikatu w celu uruchomienia lub anulowania rzeczywistej operacji podpisywania. Certyfikat wynikający z kluczem publicznym jest podpisany przez urząd certyfikacji. Wykonanie tej operacji może potrwać kilka sekund.

   ![Wyświetl certyfikat](media/howto-opc-vault-secure/view-cert-csr.png "Wyświetl certyfikat")

6. Certyfikat wynikający (DER) można pobrać z tego miejsca jako plik binarny. Dostępna jest również wersja zakodowana algorytmem Base64, na przykład w celu kopiowania wklejenia certyfikatu do wiersza polecenia lub wprowadzania tekstu. 
10. Po pobraniu i zapisaniu certyfikatu można go usunąć z usługi za pomocą `Delete Certificate` przycisku.
11. Ze względu na użycie certyfikatu podpisanego przez urząd certyfikacji należy również pobrać certyfikat urzędu certyfikacji i listę CRL.
12. Teraz zależy to od urządzenia OPC UA, jak zastosować nowy certyfikat. Zwykle certyfikat urzędu certyfikacji i listy CRL są kopiowane do `trusted` folderu, podczas gdy certyfikat aplikacji jest stosowany `own` do folderu w magazynie certyfikatów. Niektóre urządzenia mogą już obsługiwać "wypychanie" serwera dla aktualizacji certyfikatów. Zapoznaj się z dokumentacją urządzenia OPC UA.

### <a name="step-4-device-secured"></a>Krok 4: Urządzenie zabezpieczone

Urządzenie OPC UA jest teraz gotowe do komunikowania się z innymi urządzeniami OPC UA zabezpieczonymi przez podpisane certyfikaty urzędu certyfikacji bez dalszej konfiguracji. Owocnej pracy.


## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zabezpieczyć urządzenia OPC UA, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Uruchom bezpieczną usługę zarządzania certyfikatami](howto-opc-vault-secure-ca.md)