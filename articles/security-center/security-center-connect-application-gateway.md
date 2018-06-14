---
title: Połączenie bramy aplikacji Microsoft Azure do Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować bramy aplikacji i Centrum zabezpieczeń Azure, aby zwiększyć ogólne bezpieczeństwo Twoich zasobów.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: terrylan
ms.openlocfilehash: 7c15e5a86df7ff2a374aa9b62d2775b1eb035fc6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29854493"
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Połączenie bramy aplikacji Microsoft Azure do Centrum zabezpieczeń Azure
Ten dokument pomaga skonfigurować integrację z Centrum zabezpieczeń i zapory aplikacji sieci web Application Gateway (WAF).

## <a name="why-connect-application-gateway"></a>Dlaczego łączyć bramy aplikacji?
Zapory aplikacji sieci Web w aplikacji bramy chroni aplikacje sieci web przed wspólnej ataków opartych na sieci web takich jak iniekcja kodu SQL, ataki skryptów między witrynami i hijacks sesji. Centrum zabezpieczeń jest zintegrowany z bramy aplikacji, aby zapobiec i wykrywanie zagrożeń dla aplikacji sieci web niechronione w środowisku.

## <a name="how-do-i-configure-this-integration"></a>Jak skonfigurować tej integracji?
Centrum zabezpieczeń wykryje wdrożonej wcześniej wystąpień zapory aplikacji sieci Web w subskrypcji. Te rozwiązania nawiązać połączenia z Centrum zabezpieczeń, które umożliwiają integrację alertów.

> [!NOTE]
> Można też udostępnić WAF bramy aplikacji z Centrum zabezpieczeń **zalecenia** zgodnie z opisem w [Dodawanie zapory aplikacji sieci web](security-center-add-web-application-firewall.md).
>
>

1. Zaloguj się do [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. Na **menu Microsoft Azure**, wybierz pozycję **Centrum zabezpieczeń**. Zostanie otwarte okno **Security Center — Przegląd**.

3. W obszarze **omówienie**, wybierz pozycję **rozwiązań zabezpieczeń**.

  ![Omówienie Centrum zabezpieczeń](./media/security-center-connect-application-gateway/overview.png)

4. W obszarze **odnalezione rozwiązań** Microsoft zapory aplikacji sieci Web na podstawie SaaS Znajdź i zaznacz pozycję **CONNECT**.

  ![Rozwiązania odnalezione](./media/security-center-connect-application-gateway/connect.png)

5. **Połącz rozwiązania zapory aplikacji sieci Web** otwiera.  Wybierz **Connect** do integracji z Centrum zabezpieczeń i zapory aplikacji sieci Web.

  ![Łączenie rozwiązania zapory aplikacji internetowych](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób integracji WAF brama aplikacji w Centrum zabezpieczeń. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Zintegrowanie rozwiązań zabezpieczeń w Centrum zabezpieczeń](security-center-partner-integration.md)
* [Łączącego Microsoft Advanced Threat Analytics do Centrum zabezpieczeń](security-center-ata-integration.md)
* [Łączenie ochrony tożsamości usługi Azure Active Directory z Centrum zabezpieczeń](security-center-aadip-integration.md)
* [Monitorowanie kondycji zabezpieczeń w usłudze Security Center](security-center-monitoring.md).
* [Monitorowanie rozwiązań partnerskich w usłudze Security Center](security-center-partner-solutions.md).
* [Azure Security Center — często zadawane pytania](security-center-faq.md).
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/).
