---
title: Certyfikaty wymagane do zezwalania na serwery wewnętrznej bazy danych
titleSuffix: Azure Application Gateway
description: Ten artykuł zawiera przykłady sposobu konwertowania certyfikatu TLS/SSL na certyfikat uwierzytelniania i zaufany certyfikat główny, które są wymagane do zezwalania na wystąpienia wewnętrznej bazy danych w usłudze Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 20f588639c54b0a8b7cd304f33b5a9d633a73be6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133043"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Tworzenie certyfikatów w celu umożliwienia wewnętrznej bazy danych za pomocą bramy aplikacji platformy Azure

Aby wykonać end to end TLS, brama aplikacji wymaga wystąpienia wewnętrznej bazy danych, które mają być dozwolone przez przekazywanie uwierzytelniania/zaufanych certyfikatów głównych. W przypadku jednostki SKU w wersji 1 wymagane są certyfikaty uwierzytelniania, ale dla zaufanej jednostki SKU w wersji 2 wymagane są certyfikaty główne, aby zezwolić na certyfikaty.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> - Eksportowanie certyfikatu uwierzytelniania z certyfikatu wewnętrznej bazy danych (dla jednostki SKU w wersji 1)
> - Eksportowanie zaufanego certyfikatu głównego z certyfikatu wewnętrznej bazy danych (dla jednostki SKU w wersji 2)

## <a name="prerequisites"></a>Wymagania wstępne

Istniejący certyfikat wewnętrznej bazy danych jest wymagany do generowania certyfikatów uwierzytelniania lub zaufanych certyfikatów głównych wymaganych do zezwalania na wystąpienia zaplecza za pomocą bramy aplikacji. Certyfikat wewnętrznej bazy danych może być taki sam jak certyfikat TLS/SSL lub inny dla dodatkowych zabezpieczeń. Brama aplikacji nie udostępnia żadnego mechanizmu tworzenia lub zakupu certyfikatu TLS/SSL. Do celów testowych można utworzyć certyfikat z podpisem własnym, ale nie należy go używać do obciążeń produkcyjnych. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Certyfikat uwierzytelniania eksportu (dla jednostki SKU w wersji 1)

Certyfikat uwierzytelniania jest wymagany do zezwalania na wystąpienia wewnętrznej bazy danych w jednostce SKU bramy aplikacji w wersji 1. Certyfikat uwierzytelniania jest kluczem publicznym certyfikatów serwera wewnętrznej bazy danych w kodowanym kodowanym numerze X.509 bazy podstawowej 64(. CER). W tym przykładzie użyjesz certyfikatu TLS/SSL dla certyfikatu wewnętrznej bazy danych i wyeksportujesz jego klucz publiczny do użycia jako certyfikacja uwierzytelniania. Ponadto w tym przykładzie użyjesz narzędzia Menedżer certyfikatów systemu Windows, aby wyeksportować wymagane certyfikaty. Możesz użyć dowolnego innego narzędzia, które jest wygodne.

Z certyfikatu TLS/SSL wyeksportuj plik cer klucza publicznego (nie klucz prywatny). Poniższe kroki pomagają wyeksportować plik cer w kodowanym kodowanym X.509 base-64(. CER) dla certyfikatu:

1. Aby uzyskać plik cer z certyfikatu, otwórz okno **Zarządzaj certyfikatami użytkowników**. Znajdź certyfikat, zazwyczaj w "Certyfikaty — bieżący użytkownik\Osobisty\Certyfikaty" i kliknij prawym przyciskiem myszy. Kliknij pozycję **Wszystkie zadania**, a następnie kliknij pozycję **Eksportuj**. Spowoduje to otwarcie **Kreatora eksportu certyfikatów**. Jeśli nie możesz znaleźć certyfikatu w obszarze Bieżący użytkownik\Osobiste\Certyfikaty, być może przypadkowo otwarto "Certyfikaty — komputer lokalny", a nie "Certyfikaty — bieżący użytkownik"). Jeśli chcesz otworzyć Menedżera certyfikatów w bieżącym zakresie użytkownika przy użyciu programu PowerShell, wpisz *certmgr* w oknie konsoli.

   ![Eksportowanie](./media/certificates-for-backend-authentication/export.png)

2. W Kreatorze kliknij pozycję **Dalej**.

   ![Eksportowanie certyfikatu](./media/certificates-for-backend-authentication/exportwizard.png)

3. Wybierz pozycję **Nie eksportuj klucza prywatnego**, a następnie kliknij pozycję **Dalej**.

   ![Nie należy eksportować klucza prywatnego](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na stronie **Format pliku eksportu** wybierz pozycję **Certyfikat X.509 szyfrowany algorytmem Base-64 (.CER)**, a następnie kliknij pozycję **Dalej**.

   ![Zakodowane w bazie-64](./media/certificates-for-backend-authentication/base64.png)

5. W przypadku **eksportu pliku** **przejdź** do lokalizacji, do której chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **Dalej**.

   ![Browse](./media/certificates-for-backend-authentication/browse.png)

6. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

   ![Zakończ](./media/certificates-for-backend-authentication/finish.png)

7. Certyfikat został pomyślnie wyeksportowany.

   ![Powodzenie](./media/certificates-for-backend-authentication/success.png)

   Wyeksportowany certyfikat wygląda podobnie do następującego:

   ![Eksportowane](./media/certificates-for-backend-authentication/exported.png)

8. Jeśli wyeksportowany certyfikat zostanie otwarty przy użyciu Notatnika, zobaczysz coś podobnego do tego przykładu. Sekcja na niebiesko zawiera informacje, które są przekazywane do bramy aplikacji. Jeśli otworzysz certyfikat za pomocą Notatnika i nie wygląda on podobnie do tego, zazwyczaj oznacza to, że nie wyeksportowałeś go przy użyciu zakodowanego X.509 base-64(. CER). Ponadto jeśli chcesz użyć innego edytora tekstu, należy zrozumieć, że niektóre edytory mogą wprowadzać niezamierzone formatowanie w tle. Może to spowodować problemy po przekazaniu tekstu z tego certyfikatu na platformę Azure.

   ![Otwórz za pomocą Notatnika](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Eksportowanie zaufanego certyfikatu głównego (dla jednostki SKU w wersji 2)

Zaufany certyfikat główny jest wymagany do umieszczania na białej liście wystąpień zaplecza w jednostce SKU bramy aplikacji w wersji 2. Certyfikat główny jest zakodowany x.509(Base-64. CER) formatować certyfikat główny z certyfikatów serwera wewnętrznej bazy danych. W tym przykładzie użyjemy certyfikatu TLS/SSL dla certyfikatu wewnętrznej bazy danych, wyeksportujemy jego klucz publiczny, a następnie wyeksportujemy certyfikat główny zaufanego urzędu certyfikacji z klucza publicznego w formacie kodowanym base64, aby uzyskać zaufany certyfikat główny. Certyfikaty pośrednie powinny być powiązane z certyfikatem serwera i zainstalowane na serwerze wewnętrznej bazy danych.

Poniższe kroki pomagają wyeksportować plik cer dla certyfikatu:

1. Aby wyeksportować klucz publiczny z certyfikatu wewnętrznej bazy danych, należy wykonać kroki 1–9 wymienione w sekcji **Certyfikat uwierzytelniania eksportującego z certyfikatu wewnętrznej bazy danych (dla jednostki SKU w wersji 1).**

2. Po wyeksportowanie klucza publicznego otwórz plik.

   ![Otwarty certyfikat autoryzacji](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![informacje o certyfikacie](./media/certificates-for-backend-authentication/general.png)

3. Przejdź do widoku Ścieżka certyfikacji, aby wyświetlić urząd certyfikacji.

   ![szczegóły certyfikatu](./media/certificates-for-backend-authentication/certdetails.png)

4. Wybierz certyfikat główny i kliknij **wyświetl certyfikat**.

   ![ścieżka certyfikatu](./media/certificates-for-backend-authentication/rootcert.png)

   Powinny być widoczne szczegóły certyfikatu głównego.

   ![informacje o cert](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Przejdź do widoku **Szczegóły** i kliknij przycisk **Kopiuj do pliku...**

   ![kopiowanie certyfikatu głównego](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. W tym momencie wyodrębnino szczegóły certyfikatu głównego z certyfikatu wewnętrznej bazy danych. Zostanie wyświetlony **Kreator eksportu certyfikatów**. Teraz użyj kroków 2-9 wymienionych w sekcji **Certyfikat uwierzytelniania eksportującego z certyfikatu wewnętrznej bazy danych (dla jednostki SKU w wersji 1) powyżej,** aby wyeksportować zaufany certyfikat główny w zakodowanym kodach X.509 base-64(. CER).

## <a name="next-steps"></a>Następne kroki

Teraz masz certyfikat uwierzytelniania/zaufany certyfikat główny w bazie-64 zakodowany X.509(. CER). Można dodać to do bramy aplikacji, aby na białej liście serwerów wewnętrznej bazy danych do szyfrowania TLS od końca do końca. Zobacz [Konfigurowanie protokołu TLS od końca do końca przy użyciu bramy aplikacji w programie PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

