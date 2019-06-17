---
title: Zabezpieczenia usługi Azure Service łańcucha bloków
description: Azure Blockchain danych dostęp i większe bezpieczeństwo pojęcia dotyczące usługi
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dd0a33364ed9395a85478798e47352c533bd47dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028203"
---
# <a name="azure-blockchain-service-security"></a>Zabezpieczenia usługi Azure Service łańcucha bloków

Usługa Azure Blockchain korzysta kilka funkcji platformy Azure do przechowywania danych, bezpieczne i dostępne. Dane są zabezpieczane przy użyciu izolacji, szyfrowania i uwierzytelniania.

## <a name="isolation"></a>Izolacja

Zasoby platformy Azure usługa łańcucha bloków są izolowane w prywatnej sieci wirtualnej. Każdy węzeł transakcji i sprawdzanie poprawności jest maszynę wirtualną (VM). Maszyny wirtualne w jednej sieci wirtualnej nie może komunikować się bezpośrednio do maszyn wirtualnych w innej sieci wirtualnej. Izolacja zapewnia, że komunikacja pozostanie prywatne w ramach sieci wirtualnej. Aby uzyskać więcej informacji na izolację sieci wirtualnej platformy Azure, zobacz [izolacja w chmurze publicznej platformy Azure](../../security/azure-isolation.md#networking-isolation).

![Diagram sieci Wirtualnej](./media/data-security/vnet.png)

## <a name="encryption"></a>Szyfrowanie

Dane użytkownika są przechowywane w usłudze Azure storage. Dane użytkownika są szyfrowane, ruchu i przechowywanych w zakresie zabezpieczeń i poufności. Aby uzyskać więcej informacji, zobacz: [Przewodnik po zabezpieczeniach magazynu Azure](../../storage/common/storage-security-guide.md).

## <a name="authentication"></a>Authentication

Transakcje mogą być wysyłane do węzłów łańcucha bloków za pośrednictwem punktu końcowego usługi RPC. Klienci komunikować się z węzłem transakcji przy użyciu zwrotnego serwera proxy to obsługuje uwierzytelnianie użytkowników i szyfruje dane za pośrednictwem protokołu SSL.

![Diagram uwierzytelniania](./media/data-security/authentication.png)

Istnieją trzy tryby uwierzytelniania dla dostępu zdalnego wywoływania procedur.

### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Podstawowe uwierzytelnianie przy użyciu nagłówka uwierzytelniania HTTP zawierający nazwę użytkownika i hasło. Nazwa użytkownika jest nazwa węzła łańcucha bloków. Hasło jest ustawiany podczas inicjowania obsługi administracyjnej element członkowski lub węzeł. Można zmienić hasło przy użyciu witryny Azure portal lub interfejsu wiersza polecenia.

### <a name="access-keys"></a>Klucze dostępu

Klucze dostępu użyć losowo wygenerowanego ciągu objęte adres URL punktu końcowego. Dwa klucze dostępu wspomagające wymiany kluczy. Klucze można generowane z witryny Azure portal i interfejs wiersza polecenia.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Usługa Azure Active Directory (Azure AD) używa oparta na oświadczeniach mechanizm uwierzytelniania, w którym użytkownik jest uwierzytelniany przez usługę Azure AD przy użyciu poświadczeń użytkownika usługi Azure AD. Usługi Azure AD zapewnia zarządzanie tożsamościami oparta na chmurze i umożliwia klientom skorzystanie z jednej tożsamości w całej aplikacji przedsiębiorstwa i dostępem w chmurze. Usługa Azure Blockchain integruje się z usługą Azure AD, włączenie Federacji identyfikator, pojedynczego logowania jednokrotnego oraz uwierzytelnianie wieloskładnikowe. Możesz przypisać użytkownikom, grupom i ról aplikacji w Twojej organizacji, uzyskać dostęp do elementu członkowskiego i języka node łańcucha bloków.

Serwer proxy klienta usługi Azure AD jest dostępna w [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). Serwer proxy klienta przekierowuje użytkownika do strony logowania usługi Azure AD i uzyskuje token elementu nośnego po pomyślnym uwierzytelnieniu. Następnie użytkownik Ethereum aplikacji klienckiej, takich jak Geth lub Truffle łączy się z punktu końcowego serwera proxy klienta. Na koniec po przesłaniu transakcji, serwer proxy klienta wprowadza tokenu elementu nośnego w nagłówku http i zwrotny serwer proxy sprawdza poprawność tokenu przy użyciu protokołu OAuth.

## <a name="keys-and-ethereum-accounts"></a>Klucze i Ethereum kont

Podczas aprowizowania elementu członkowskiego usługi Azure Service łańcucha bloków, generowany jest konto Ethereum i parę kluczy publicznych i prywatnych. Klucz prywatny jest używany do wysyłania transakcji do łańcucha bloków. Konto Ethereum jest ostatnie 20 bajtów skrótu klucza publicznego. Konto Ethereum jest również nazywany portfela.

Pary kluczy prywatnych i publicznych jest przechowywana keyfile w formacie JSON. Klucz prywatny są szyfrowane przy użyciu hasła wprowadzonego po utworzeniu usługi Rejestr łańcucha bloków.

Klucze prywatne są używane do cyfrowego podpisywania transakcji. W łańcuchy prywatnego kontrakt inteligentne podpisane przez klucz prywatny reprezentuje tożsamość osoby podpisującej. Aby sprawdzić poprawność podpisu, odbiornik można porównać klucza publicznego osoby podpisującej przy użyciu adresu obliczane na podstawie podpisu.

Constellation klucze są używane do unikatowego identyfikowania węzłów kworum. Constellation klucze są generowane w czasie inicjowania obsługi administracyjnej węzłów i są określone w parametrze privateFor prywatnej transakcji w kworum.

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie węzłów transakcji usługi Azure Service łańcucha bloków](configure-transaction-nodes.md)
