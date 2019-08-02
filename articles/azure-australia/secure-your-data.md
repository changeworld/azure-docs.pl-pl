---
title: Bezpieczeństwo danych w Australii platformy Azure
description: Skonfigurowanie platformy Azure w regionach australijskich w celu spełnienia określonych wymagań australijskich zasad obowiązujących dla instytucji rządowych, przepisów i przepisów prawnych.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0301d506fc5764dbfda496727da95b2a1f2e9c7e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608353"
---
# <a name="data-security-in-azure-australia"></a>Bezpieczeństwo danych w Australii platformy Azure

Zasady dotyczące przeniesień do zabezpieczania danych klienta to:

* Ochrona danych przy użyciu szyfrowania
* Zarządzanie wpisami tajnymi
* Ograniczanie dostępu do danych

## <a name="encrypting-your-data"></a>Szyfrowanie danych

Szyfrowanie danych może być stosowane na poziomie dysku (w spoczynku), w bazach danych (w trybie spoczynku i w tranzycie), w aplikacjach (w trakcie przesyłania) i w sieci (w tranzycie). Istnieje kilka sposobów na uzyskanie szyfrowania na platformie Azure:

|Usługa/funkcja|Opis|
|---|---|
|Szyfrowanie usługi Storage|Usługa Azure szyfrowanie usługi Storage jest włączona na poziomie konta magazynu, dzięki czemu blokowe obiekty blob i stronicowe obiekty blob są automatycznie szyfrowane podczas zapisywania do usługi Azure Storage. Podczas odczytywania danych z usługi Azure Storage zostanie ono odszyfrowane przez usługę magazynu przed zwróceniem. Używaj instrukcji SSE do zabezpieczania danych bez konieczności modyfikowania i dodawania kodu do dowolnych aplikacji.|
|Azure Disk Encryption|Użyj Azure Disk Encryption, aby zaszyfrować dyski systemu operacyjnego i dyski danych używane przez maszynę wirtualną platformy Azure. Integracja z Azure Key Vault zapewnia kontrolę i ułatwia zarządzanie kluczami szyfrowania dysków.|
|Szyfrowanie na poziomie aplikacji po stronie klienta|Szyfrowanie po stronie klienta jest wbudowane w środowisko Java i biblioteki klienckie programu .NET Storage, które mogą korzystać z Azure Key Vault interfejsów API, co sprawia, że jest to proste do wdrożenia. Użyj Azure Key Vault, aby uzyskać dostęp do wpisów tajnych w Azure Key Vault dla konkretnych osób przy użyciu Azure Active Directory.|
|Szyfrowanie podczas przesyłania|Podstawowe szyfrowanie dostępne na potrzeby łączności z platformą Azure Australia obsługuje protokół TLS (Transport Level Security) 1,2 i certyfikaty X. 509. Algorytmy kryptograficzne FIPS (Federal Information Processing Standard) 140-2 na poziomie 1 są również używane w przypadku połączeń sieciowych infrastruktury między centrami danych platformy Azure w Australii.  Systemy Windows Server 2016, Windows 10, Windows Server 2012 R2, Windows 8.1 i udziały plików platformy Azure mogą używać protokołu SMB 3,0 do szyfrowania między maszyną wirtualną a udziałem plików. Szyfrowanie po stronie klienta umożliwia szyfrowanie danych przed ich przesłaniem do magazynu w aplikacji klienckiej oraz odszyfrowywanie danych po ich przeniesieniu poza magazyn.|
|Maszyny wirtualne IaaS|Użyj Azure Disk Encryption. Włącz szyfrowanie usługi Storage, aby zaszyfrować pliki VHD, które są używane do tworzenia kopii zapasowych dysków w usłudze Azure Storage, ale tylko szyfruje nowo zapisywane dane. Oznacza to, że jeśli utworzysz maszynę wirtualną, a następnie włączysz szyfrowanie usługi Storage na koncie magazynu zawierającym plik VHD, tylko te zmiany będą szyfrowane, a nie oryginalny plik VHD.|
|Szyfrowania po stronie klienta|Jest to najbezpieczniejsza Metoda szyfrowania danych, ponieważ szyfruje ją przed przekazaniem i szyfruje dane przechowywane w stanie spoczynku. Jednak wymaga to dodania kodu do aplikacji przy użyciu usługi Storage, co może nie być konieczne. W takich przypadkach można użyć protokołu HTTPS do przesyłania danych, a szyfrowanie usługi Storage do szyfrowania danych przechowywanych w spoczynku. Szyfrowanie po stronie klienta obejmuje również więcej obciążeń na kliencie — należy je uwzględnić w planach skalowalności, zwłaszcza w przypadku szyfrowania i przenoszenia dużych ilości danych.|
|

Aby uzyskać więcej informacji na temat opcji szyfrowania na platformie Azure, zobacz [Przewodnik po zabezpieczeniach magazynu](https://docs.microsoft.com/azure/storage/storage-security-guide).

## <a name="protecting-data-by-managing-secrets"></a>Ochrona danych przez zarządzanie wpisami tajnymi

Bezpieczne zarządzanie kluczami jest niezbędne do ochrony danych w chmurze. Klienci powinni dążyć do uproszczenia zarządzania kluczami i zapewnienia kontroli nad kluczami używanymi przez aplikacje i usługi w chmurze do szyfrowania danych.

### <a name="managing-secrets"></a>Zarządzanie wpisami tajnymi

* Użyj Key Vault, aby zminimalizować ryzyko wystąpienia tajnych ujawniane za pomocą zakodowanych plików konfiguracji, skryptów lub kodu źródłowego. Azure Key Vault szyfruje klucze (takie jak klucze szyfrowania dla Azure Disk Encryption) i wpisy tajne (takie jak hasła), przechowując je w standardzie FIPS 140-2 Level 2 sprawdzone sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń). Aby uzyskać gwarancję, można importować lub generować klucze w tych sprzętowych modułów zabezpieczeń.
* Kod i szablony aplikacji powinny zawierać tylko odwołania identyfikatorów URI do wpisów tajnych (oznacza to, że rzeczywiste wpisy tajne nie znajdują się w kodzie, konfiguracji ani repozytoriów kodu źródłowego). Zapobiega to atakom polegającym na wyłudzaniu kluczowych informacji na temat wewnętrznych lub zewnętrznych repozytoriów, takich jak zbiory botów w usłudze GitHub.
* Użyj silnych kontrolek RBAC w Key Vault. Jeśli zaufany operator opuści firmę lub przeniesie ją do nowej grupy w ramach firmy, nie powinien mieć możliwości dostępu do wpisów tajnych.  

Aby uzyskać więcej informacji, zobacz [Azure Key Vault](azure-key-vault.md)

## <a name="isolation-to-restrict-data-access"></a>Izolacja w celu ograniczenia dostępu do danych

Izolacja polega na wykorzystaniu granic, segmentacji i kontenerów w celu ograniczenia dostępu do danych tylko do autoryzowanych użytkowników, usług i aplikacji. Na przykład rozdzielenie między dzierżawcami jest podstawowym mechanizmem zabezpieczeń dla wielodostępnych platform w chmurze, takich jak Microsoft Azure. Izolacja logiczna pomaga uniemożliwić jednej dzierżawie zakłócanie operacji innych dzierżawców.

#### <a name="per-customer-isolation"></a>Izolacja wg klienta

Platforma Azure implementuje kontrolę dostępu do sieci i segregację za pomocą izolacji sieci VLAN warstwy 2, list kontroli dostępu, modułów równoważenia obciążenia i filtrów IP.

Klienci mogą dodatkowo izolować swoje zasoby między subskrypcjami, grupami zasobów, sieciami wirtualnymi i podsieciami.

Aby uzyskać więcej informacji na temat izolacji w Microsoft Azure, zobacz [izolacja w chmurze publicznej platformy Azure](../security/fundamentals/isolation-choices.md).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem na [platformie Azure VPN Gateway](vpn-gateway.md)