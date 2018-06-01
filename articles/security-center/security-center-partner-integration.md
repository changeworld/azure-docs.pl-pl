---
title: Integracja rozwiązań zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Poznaj sposób integracji usługi Azure Security Center z partnerami w celu poprawy ogólnego stanu zabezpieczeń zasobów platformy Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/16/2018
ms.author: terrylan
ms.openlocfilehash: 28bb900a3281becc7bb243e6bd7bf2beedf1a5c1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261635"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integracja rozwiązań zabezpieczeń w usłudze Azure Security Center
Ten dokument ułatwia zarządzanie rozwiązaniami zabezpieczeń już połączonymi z usługą Azure Security Center i dodawanie nowych.

## <a name="integrated-azure-security-solutions"></a>Zintegrowane rozwiązania zabezpieczeń platformy Azure
Usługa Security Center ułatwia włączanie zintegrowanych rozwiązań zabezpieczeń na platformie Azure. Korzyści to:

- **Uproszczone wdrażanie**: usługa Security Center oferuje udoskonaloną aprowizację zintegrowanych rozwiązań partnerskich. W przypadku rozwiązań takich jak ocena ochrony przed złośliwym oprogramowaniem i luk w zabezpieczeniach usługa Security Center może aprowizować wymaganego agenta na Twoich maszynach wirtualnych, zaś dla urządzeń zapory usługa Security Center określa większość wymaganej konfiguracji sieci.
- **Zintegrowane funkcje wykrywania**: zdarzenia zabezpieczeń z rozwiązań partnerskich są automatycznie zbierane, agregowane i wyświetlane w ramach zdarzeń i alertów usługi Security Center. Te zdarzenia są także połączone z funkcjami wykrywania z innych źródeł, aby zapewnić zaawansowane możliwości w zakresie wykrywania zagrożeń.
- **Ujednolicone zarządzanie monitorowaniem kondycji**: klienci mogą używać zintegrowanych zdarzeń kondycji do błyskawicznego monitorowania wszystkich rozwiązań partnerskich. Podstawowe funkcje zarządzania zapewniają łatwy dostęp do konfiguracji zaawansowanej przy użyciu rozwiązania partnerskiego.

Obecnie zintegrowane rozwiązania zabezpieczeń obejmują:

- Ochrona punktów końcowych ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/search/result.aspx?q=Windows+defender+endpoint+protection) i [System Center Endpoint Protection](https://www.microsoft.com/search/result.aspx?q=System+Center+endpoint+protection))
- Zapora aplikacji sieci Web ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/products.html) i [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Zapora nowej generacji ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2), [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html) i [Palo Alto Networks](https://www.paloaltonetworks.com/products))
- Ocena luk w zabezpieczeniach ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Obsługa integracji ochrony punktów końcowych może się różnić w zależności od rozwiązania. W poniższej tabeli zamieszczono więcej informacji na temat obsługi poszczególnych rozwiązań:

| Ochrona punktów końcowych               | Platformy                             | Instalacja usługi Security Center | Odnajdywanie usługi Security Center |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Windows Defender (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem)                  | Windows Server 2016                   | Nie, wbudowana w system operacyjny           | Yes                       |
| System Center Endpoint Protection (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem) | Windows Server 2012 R2, 2012, 2008 R2 | Za pomocą rozszerzenia                | Yes                       |
| Trend Micro — wszystkie wersje         | Rodzina systemów Windows Server                 | Za pomocą rozszerzenia                | Yes                       |
| Symantec — w wersji co najmniej 12.1.1100              | Rodzina systemów Windows Server                 | Nie                           | Yes                       |
| McAfee — w wersji co najmniej 10                       | Rodzina systemów Windows Server                 | Nie                           | Yes                       |
| Kaspersky                         | Rodzina systemów Windows Server                 | Nie                           | Nie                        |
| Sophos                            | Rodzina systemów Windows Server                 | Nie                           | Nie                        |



## <a name="how-security-solutions-are-integrated"></a>Jak są integrowane rozwiązania zabezpieczeń
Rozwiązania zabezpieczeń platformy Azure, które zostały wdrożone z usługi Security Center, są automatycznie połączone. Możesz także połączyć inne źródła danych zabezpieczeń, a w tym:

- Usługa Azure AD Identity Protection
- Komputery działające lokalnie lub w innych chmurach
- Rozwiązanie zabezpieczeń, które obsługuje format Common Event Format (CEF)
- Microsoft Advanced Threat Analytics

![Integracja rozwiązań partnerskich](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Zarządzanie zintegrowanymi rozwiązaniami zabezpieczeń platformy Azure i innymi źródłami danych

1. Zaloguj się w [Portalu Azure](https://azure.microsoft.com/features/azure-portal/).

2. W **menu platformy Microsoft Azure** wybierz pozycję **Security Center**. Zostanie otwarte okno **Security Center — Przegląd**.

  ![Security Center — Przegląd](./media/security-center-partner-integration/overview.png)

3. W obszarze **Przegląd** wybierz pozycję **Rozwiązania w zakresie bezpieczeństwa**.

W obszarze **Rozwiązania w zakresie bezpieczeństwa** możesz wyświetlić informacje o kondycji zintegrowanych rozwiązań zabezpieczeń platformy Azure i wykonać podstawowe zadania zarządzania. Możesz też połączyć inne typy źródeł danych zabezpieczeń, takie jak alerty usługi Azure Active Directory Identity Protection, i dzienniki zapory w formacie CEF.

### <a name="connected-solutions"></a>Rozwiązania połączone

Sekcja **Połączone rozwiązania** zawiera rozwiązania zabezpieczeń, które są aktualnie połączone z usługą Security Center, i informacje o stanie kondycji poszczególnych rozwiązań.  

![Rozwiązania połączone](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Aby dowiedzieć się więcej, zobacz [Managing connected partner solutions (Zarządzanie połączonymi rozwiązaniami partnerskimi)](security-center-partner-solutions.md).

### <a name="discovered-solutions"></a>Rozwiązania odnalezione

Usługa Security Center automatycznie odnajduje rozwiązania w zakresie zabezpieczeń działające na platformie Azure, ale niepołączone z usługą Security Center, i wyświetla rozwiązania w sekcji **Rozwiązania odnalezione**. Obejmuje to rozwiązania platformy Azure, takie jak [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), jak również rozwiązania partnerskie.

> [!NOTE]
> Funkcja odnalezionych rozwiązań wymaga usługi Security Center w warstwie Standardowa na poziomie subskrypcji. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych zabezpieczeń.
>
>

Wybierz pozycję **POŁĄCZ** w obszarze rozwiązania, aby zintegrować je z usługą Security Center i otrzymywać powiadomienia o alertach zabezpieczeń.

![Rozwiązania odnalezione](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Usługa Security Center odnajduje również rozwiązania wdrożone w ramach subskrypcji, które mogą przekazywać dalej Dzienniki w formacie Common Event Format (CEF). Dowiedz się, jak [połączyć rozwiązanie w zakresie zabezpieczeń](quick-security-solutions.md), które używa dzienników CEF, z usługą Security Center.

### <a name="add-data-sources"></a>Dodawanie źródeł danych

Sekcja **Dodawanie źródeł danych** obejmuje inne dostępne źródła danych, które mogą zostać połączone. Aby uzyskać instrukcje dotyczące dodawania danych z dowolnego z tych źródeł, kliknij przycisk **DODAJ**.

![Źródła danych](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób zintegrowania rozwiązania partnerskiego w usłudze Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Connecting Microsoft Advanced Threat Analytics to Azure Security Center (Łączenie usługi Microsoft Advanced Threat Analytics z usługą Azure Security Center)](security-center-ata-integration.md)
* [Connecting Azure Active Directory Identity Protection to Azure Security Center (Łączenie usługi Azure Active Directory Identity Protection z usługą Azure Security Center)](security-center-aadip-integration.md)
* [Monitorowanie kondycji zabezpieczeń w usłudze Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Monitorowanie rozwiązań partnerskich w usłudze Security Center](security-center-partner-solutions.md). Informacje na temat sposobu monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi Security Center.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
