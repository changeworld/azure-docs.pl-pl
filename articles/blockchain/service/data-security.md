---
title: Zabezpieczenia usługi Azure Blockchain
description: Pojęcia dotyczące dostępu do danych i zabezpieczeń usługi Azure Blockchain
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 06bf4e0fa4037b07505a4f816fc7af56c14576d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982225"
---
# <a name="azure-blockchain-service-security"></a>Zabezpieczenia usługi Azure Blockchain

Usługa Azure Blockchain service korzysta z kilku funkcji platformy Azure, aby zapewnić bezpieczeństwo i dostęp danych. Dane są zabezpieczone przy użyciu izolacji, szyfrowania i uwierzytelniania.

## <a name="isolation"></a>Izolacja

Zasoby usługi Azure Blockchain Service są izolowane w prywatnej sieci wirtualnej. Każdy węzeł transakcji i sprawdzania poprawności jest maszyną wirtualną (VM). Maszyny wirtualne w jednej sieci wirtualnej nie mogą komunikować się bezpośrednio z maszynami wirtualnymi w innej sieci wirtualnej. Izolacja zapewnia, że komunikacja pozostaje prywatna w sieci wirtualnej. Aby uzyskać więcej informacji na temat izolacji sieci wirtualnej platformy Azure, zobacz [izolację w chmurze publicznej platformy Azure](../../security/fundamentals/isolation-choices.md#networking-isolation).

![Diagram sieci wirtualnej](./media/data-security/vnet.png)

## <a name="encryption"></a>Szyfrowanie

Dane użytkownika są przechowywane w magazynie platformy Azure. Dane użytkownika są szyfrowane w ruchu i w spoczynku ze względów bezpieczeństwa i poufności. Aby uzyskać więcej informacji, zobacz: [Przewodnik po zabezpieczeniach usługi Azure Storage](../../storage/blobs/security-recommendations.md).

## <a name="authentication"></a>Uwierzytelnianie

Transakcje mogą być wysyłane do węzłów łańcucha bloków za pośrednictwem punktu końcowego RPC. Klienci komunikują się z węzłem transakcji przy użyciu odwrotnego serwera proxy, który obsługuje uwierzytelnianie użytkowników i szyfruje dane za pośrednictwem SSL.

![Diagram uwierzytelniania](./media/data-security/authentication.png)

Istnieją trzy tryby uwierzytelniania dla dostępu RPC.

### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Uwierzytelnianie podstawowe używa nagłówka uwierzytelniania HTTP zawierającego nazwę użytkownika i hasło. Nazwa użytkownika to nazwa węzła łańcucha bloków. Hasło jest ustawiane podczas inicjowania obsługi administracyjnej elementu członkowskiego lub węzła. Hasło można zmienić za pomocą witryny Azure portal lub interfejsu wiersza polecenia.

### <a name="access-keys"></a>Klawisze dostępu

Klucze dostępu używają losowo wygenerowanego ciągu zawartego w adresie URL punktu końcowego. Dwa klawisze dostępu ułatwią obracanie klawiszy. Klucze można ponowniegenerować z witryny Azure portal i interfejsu wiersza polecenia.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Usługa Azure Active Directory (Azure AD) używa mechanizmu uwierzytelniania opartego na oświadczeniach, w którym użytkownik jest uwierzytelniany przez usługę Azure AD przy użyciu poświadczeń użytkownika usługi Azure AD. Usługa Azure AD zapewnia oparte na chmurze zarządzanie tożsamościami i umożliwia klientom używanie jednej tożsamości w całym przedsiębiorstwie i uzyskiwanie dostępu do aplikacji w chmurze. Usługa Azure Blockchain Service integruje się z usługą Azure AD, umożliwiając federację identyfikatorów, logowanie jednokrotne i uwierzytelnianie wieloskładnikowe. Można przypisać użytkownikom, grupom i roli aplikacji w organizacji dla dostępu do elementów członkowskich łańcucha bloków i węzłów.

Serwer proxy klienta usługi Azure AD jest dostępny w [usłudze GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). Serwer proxy klienta kieruje użytkownika do strony logowania usługi Azure AD i uzyskuje token na okaziciela po pomyślnym uwierzytelnieniu. Następnie użytkownik łączy aplikację kliencką Ethereum, taką jak Geth lub Truffle, z punktem końcowym serwera proxy klienta. Na koniec po przesłaniu transakcji serwer proxy klienta wstrzykuje token nośnika w nagłówku http, a serwer proxy odwrotnej sprawdza poprawność tokenu przy użyciu protokołu OAuth.

## <a name="keys-and-ethereum-accounts"></a>Klucze i konta Ethereum

Podczas inicjowania obsługi administracyjnej elementu członkowskiego usługi Azure Blockchain jest generowany konto Ethereum oraz para kluczy publicznych i prywatnych. Klucz prywatny służy do wysyłania transakcji do łańcucha bloków. Konto Ethereum jest ostatnim 20 bajtami skrótu klucza publicznego. Konto Ethereum jest również nazywane portfelem.

Para kluczy prywatnych i publicznych jest przechowywana jako plik kluczy w formacie JSON. Klucz prywatny jest szyfrowany przy użyciu hasła wprowadzonego podczas tworzenia usługi księgi blockchain.

Klucze prywatne służą do cyfrowego podpisywania transakcji. W prywatnych łańcuchach bloków inteligentna umowa podpisana za pomocą klucza prywatnego reprezentuje tożsamość sygnatariusza. Aby sprawdzić ważność podpisu, odbiorca może porównać klucz publiczny sygnatariusza z adresem obliczonym z podpisu.

Klucze konstelacji służą do jednoznacznej identyfikacji węzła Kworum. Klucze konstelacji są generowane w czasie inicjowania obsługi administracyjnej węzła i są określone w privateFor parametru prywatnej transakcji w Kworum.

## <a name="next-steps"></a>Następne kroki

Zobacz [Jak skonfigurować dostęp usługi Azure Active Directory dla usługi Azure Blockchain .](configure-aad.md)
