---
title: Łączenie usługi Microsoft Azure Application Gateway do usługi Azure Security Center | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować Application Gateway oraz Azure Security Center w celu ulepszenia ogólnego stanu zabezpieczeń zasobów.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: acb010b0169fd876bf540f7a4115dbabfeda923a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227199"
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Łączenie usługi Microsoft Azure Application Gateway do usługi Azure Security Center
Ten dokument pomoże Ci skonfigurować integrację z Centrum zabezpieczeń i zapory aplikacji sieci web Application Gateway (WAF).

## <a name="why-connect-application-gateway"></a>Dlaczego łączy się usługa Application Gateway?
Zapora aplikacji sieci Web w usłudze Application Gateway chroni aplikacje sieci web z typowych ataków opartych na sieci web, takimi jak iniekcja SQL, ataki z użyciem skryptów między witrynami i przechwytywanie sesji. Usługa Security Center integruje się z usługą Application Gateway, aby zapobiec i wykrywanie zagrożeń dla niechronionych aplikacji internetowych w danym środowisku.

## <a name="how-do-i-configure-this-integration"></a>Jak skonfigurować tę integrację?
Usługa Security Center odnajduje wdrożonego wcześniej wystąpienia zapory aplikacji sieci Web w ramach subskrypcji. Połącz te rozwiązania do usługi Security Center, które umożliwiają integrację alertów.

> [!NOTE]
> Brama aplikacji zapory aplikacji internetowych, także mogą być udostępniane w usłudze Security Center **zalecenia** zgodnie z opisem w [Dodaj zaporę aplikacji sieci web](security-center-add-web-application-firewall.md).
>
>

1. Zaloguj się do [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. W **menu platformy Microsoft Azure** wybierz pozycję **Security Center**.

3. W obszarze **higieny zabezpieczeń ZASOBU**, wybierz opcję **rozwiązania w zakresie bezpieczeństwa**.

  ![Security Center — Przegląd](./media/security-center-connect-application-gateway/overview.png)

4. W obszarze **rozwiązania odnalezione** Znajdź firmy Microsoft opartych na rozwiązaniach SaaS zapory aplikacji sieci Web i wybierz **CONNECT**.

  ![Rozwiązania odnalezione](./media/security-center-connect-application-gateway/connect.png)

5. **Łączenie rozwiązania zapory aplikacji sieci Web** zostanie otwarty.  Wybierz **Connect** integracji zapory aplikacji sieci Web i usługi Security Center.

  ![Łączenie rozwiązania zapory aplikacji internetowych](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób zintegrowania Application Gateway zapory aplikacji sieci Web w usłudze Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Integracja rozwiązań zabezpieczeń w usłudze Security Center](security-center-partner-integration.md)
* [Połączenie Microsoft Advanced Threat Analytics do usługi Security Center](security-center-ata-integration.md)
* [Proces łączenia usługi Azure Active Directory Identity Protection do usługi Security Center](security-center-aadip-integration.md)
* [Monitorowanie kondycji zabezpieczeń w usłudze Security Center](security-center-monitoring.md).
* [Monitorowanie rozwiązań partnerskich w usłudze Security Center](security-center-partner-solutions.md).
* [Azure Security Center — często zadawane pytania](security-center-faq.md).
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/).
