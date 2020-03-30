---
title: Adresy IP zarządzania usługą Azure HDInsight
description: Dowiedz się, z jakich adresów IP należy zezwolić na ruch przychodzący, aby prawidłowo skonfigurować sieciowe grupy zabezpieczeń i trasy zdefiniowane przez użytkownika dla sieci wirtualnej za pomocą usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: 5e8f39b58f258742108fe323d9395efd87bc288f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271805"
---
# <a name="hdinsight-management-ip-addresses"></a>Adresy IP zarządzania hdinsight

> [!Important]
> W większości przypadków można teraz użyć funkcji [znacznika usługi](hdinsight-service-tags.md) dla sieciowych grup zabezpieczeń, zamiast ręcznie dodawać adresy IP. Nowe regiony zostaną dodane tylko dla tagów usług, a statyczne adresy IP zostaną ostatecznie przestarzałe.

Jeśli używasz sieciowych grup zabezpieczeń (NSG) lub tras zdefiniowanych przez użytkownika (UDR) do kontrolowania ruchu przychodzącego do klastra USŁUGI HDInsight, należy upewnić się, że klaster może komunikować się z krytycznymi usługami kondycji i zarządzania platformy Azure.  Niektóre adresy IP dla tych usług są specyficzne dla regionu, a niektóre z nich mają zastosowanie do wszystkich regionów platformy Azure. Może być również konieczne zezwolenie na ruch z usługi Azure DNS, jeśli nie jest używany niestandardowy serwer DNS.

W poniższych sekcjach omówiono określone adresy IP, które muszą być dozwolone.

## <a name="azure-dns-service"></a>Usługa Azure DNS

Jeśli korzystasz z usługi DNS dostarczonej przez platformę Azure, zezwalaj na dostęp od __168.63.129.16__ na porcie 53. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentu. Jeśli używasz niestandardowego systemu DNS, pomiń ten krok.

## <a name="health-and-management-services-all-regions"></a>Usługi w zakresie zdrowia i zarządzania: wszystkie regiony

Zezwalaj na ruch z następujących adresów IP dla usług kondycji i zarządzania usługi Azure HDInsight, które mają zastosowanie do wszystkich regionów platformy Azure:

| Źródłowy adres IP | Element docelowy  | Kierunek |
| ---- | ----- | ----- |
| 168.61.49.99 | \*:443 | Przychodzący |
| 23.99.5.239 | \*:443 | Przychodzący |
| 168.61.48.131 | \*:443 | Przychodzący |
| 138.91.141.162 | \*:443 | Przychodzący |

## <a name="health-and-management-services-specific-regions"></a>Usługi w zakresie zdrowia i zarządzania: określone regiony

Zezwalaj na ruch z adresów IP wymienionych dla usług kondycji i zarządzania usługi Azure HDInsight w określonym regionie platformy Azure, w którym znajdują się zasoby:

> [!IMPORTANT]  
> Jeśli region platformy Azure, którego używasz, nie jest wymieniony, użyj funkcji [tagu usługi](hdinsight-service-tags.md) dla sieciowych grup zabezpieczeń.

| Kraj | Region | Dozwolone źródłowe adresy IP | Dozwolony cel podróży | Kierunek |
| ---- | ---- | ---- | ---- | ----- |
| Azja | Azja Wschodnia | 23.102.235.122</br>52.175.38.134 | \*:443 | Przychodzący |
| &nbsp; | Azja Południowo-Wschodnia | 13.76.245.160</br>13.76.136.249 | \*:443 | Przychodzący |
| Australia | Australia Wschodnia | 104.210.84.115</br>13.75.152.195 | \*:443 | Przychodzący |
| &nbsp; | Australia Południowo-Wschodnia | 13.77.2.56</br>13.77.2.94 | \*:443 | Przychodzący |
| Brazylia | Brazylia Południowa | 191.235.84.104</br>191.235.87.113 | \*:443 | Przychodzący |
| Kanada | Kanada Wschodnia | 52.229.127.96</br>52.229.123.172 | \*:443 | Przychodzący |
| &nbsp; | Kanada Środkowa | 52.228.37.66</br>52.228.45.222 |\*: 443 | Przychodzący |
| Chiny | Chiny Północne | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*:443 | Przychodzący |
| &nbsp; | Chiny Wschodnie | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*:443 | Przychodzący |
| &nbsp; | Chiny Północne 2 | 40.73.37.141</br>40.73.38.172 | \*:443 | Przychodzący |
| &nbsp; | Chiny Wschodnie 2 | 139.217.227.106</br>139.217.228.187 | \*:443 | Przychodzący |
| Europa | Europa Północna | 52.164.210.96</br>13.74.153.132 | \*:443 | Przychodzący |
| &nbsp; | Europa Zachodnia| 52.166.243.90</br>52.174.36.244 | \*:443 | Przychodzący |
| Francja | Francja Środkowa| 20.188.39.64</br>40.89.157.135 | \*:443 | Przychodzący |
| Niemcy | Niemcy Środkowe | 51.4.146.68</br>51.4.146.80 | \*:443 | Przychodzący |
| &nbsp; | Niemcy Północno-Wschodnie | 51.5.150.132</br>51.5.144.101 | \*:443 | Przychodzący |
| Indie | Indie Środkowe | 52.172.153.209</br>52.172.152.49 | \*:443 | Przychodzący |
| &nbsp; | Indie Południowe | 104.211.223.67<br/>104.211.216.210 | \*:443 | Przychodzący |
| Japonia | Japonia Wschodnia | 13.78.125.90</br>13.78.89.60 | \*:443 | Przychodzący |
| &nbsp; | Japonia Zachodnia | 40.74.125.69</br>138.91.29.150 | \*:443 | Przychodzący |
| Korea | Korea Środkowa | 52.231.39.142</br>52.231.36.209 | \*:443 | Przychodzący |
| &nbsp; | Korea Południowa | 52.231.203.16</br>52.231.205.214 | \*:443 | Przychodzący
| Wielka Brytania | Zachodnie Zjednoczone Królestwo | 51.141.13.110</br>51.141.7.20 | \*:443 | Przychodzący |
| &nbsp; | Południowe Zjednoczone Królestwo | 51.140.47.39</br>51.140.52.16 | \*:443 | Przychodzący |
| Stany Zjednoczone | Środkowe stany USA | 13.89.171.122</br>13.89.171.124 | \*:443 | Przychodzący |
| &nbsp; | Wschodnie stany USA | 13.82.225.233</br>40.71.175.99 | \*:443 | Przychodzący |
| &nbsp; | Północno-środkowe stany USA | 157.56.8.38</br>157.55.213.99 | \*:443 | Przychodzący |
| &nbsp; | Zachodnio-środkowe stany USA | 52.161.23.15</br>52.161.10.167 | \*:443 | Przychodzący |
| &nbsp; | Zachodnie stany USA | 13.64.254.98</br>23.101.196.19 | \*:443 | Przychodzący |
| &nbsp; | Zachodnie stany USA 2 | 52.175.211.210</br>52.175.222.222 | \*:443 | Przychodzący |
| &nbsp; | Zjednoczone Emiraty Północne | 65.52.252.96</br>65.52.252.97 | \*:443 | Przychodzący |

Aby uzyskać informacje na temat adresów IP do użycia w usłudze Azure Dla Instytucji Rządowych, zobacz dokument [usługi Azure Government Intelligence + Analytics.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics)

Aby uzyskać więcej informacji, zobacz sekcję [Kontrolowanie ruchu sieciowego.](hdinsight-plan-virtual-network-deployment.md#networktraffic)

Jeśli używasz tras zdefiniowanych przez użytkownika (UDR), należy określić trasę i zezwolić na ruch wychodzący z sieci wirtualnej do powyższych adresów IP z następnym przeskokiem ustawionym na "Internet".

## <a name="next-steps"></a>Następne kroki

* [Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md)
* [Tagi usługi sieciowej grupy zabezpieczeń (NSG) dla usługi Azure HDInsight](hdinsight-service-tags.md)
