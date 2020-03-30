---
title: Rozwiązywanie problemów z dostępem warunkowym przy użyciu narzędzia Co jeśli — Usługa Azure Active Directory
description: Gdzie znaleźć zasady dostępu warunkowego i dlaczego
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 07/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661afc08c76c6cde61b02a29a55b4a8bec932e21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73175812"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Rozwiązywanie problemów z dostępem warunkowym za pomocą narzędzia Co jeśli

[Narzędzie Co jeśli](what-if-tool.md) w dostępie warunkowym jest zaawansowane podczas próby zrozumienia, dlaczego zasada została lub nie została zastosowana do użytkownika w określonych okolicznościach lub jeśli zasady będą stosowane w znanym stanie.

Narzędzie Co jeśli znajduje się w **witrynie Azure portal** > **Azure Active Directory** > **Dostęp** > warunkowy Co**jeśli**.

![Warunkowe narzędzie Co zrobić jeśli w stanie domyślnym](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> Narzędzie Co jeśli obecnie nie ocenia zasad w trybie tylko do raportu.

## <a name="gathering-information"></a>Gromadzenie informacji

Narzędzie Co jeśli wymaga tylko **użytkownika,** aby rozpocząć. 

Następujące dodatkowe informacje są opcjonalne, ale pomogą zawęzić zakres dla konkretnych przypadków.

* Aplikacje w chmurze lub akcje
* Adres IP 
* Kraj
* Platforma urządzeń
* Aplikacje klienckie (wersja zapoznawcza)
* Stan urządzenia (wersja zapoznawcza) 
* Ryzyko logowania

Te informacje można zbierać od użytkownika, ich urządzenia lub dziennika logowania usługi Azure AD.

## <a name="generating-results"></a>Generowanie wyników

Wprowadź kryteria zebrane w poprzedniej sekcji i wybierz opcję **Co zrobić, aby** wygenerować listę wyników. 

W dowolnym momencie można wybrać **opcję Resetuj,** aby wyczyścić wszelkie dane wejściowe kryteriów i powrócić do stanu domyślnego.

## <a name="evaluating-results"></a>Ocena wyników

### <a name="policies-that-will-apply"></a>Zasady, które będą miały zastosowanie

Ta lista pokaże, które zasady dostępu warunkowego będą miały zastosowanie, biorąc pod uwagę warunki. Lista będzie zawierać zarówno formantów dotacji i sesji, które mają zastosowanie. Przykłady obejmują wymaganie uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do określonej aplikacji.

### <a name="policies-that-will-not-apply"></a>Zasady, które nie będą miały zastosowania

Na tej liście zostaną wyświetlone zasady dostępu warunkowego, które nie miałyby zastosowania, jeśli zostaną zastosowane warunki. Lista będzie zawierać wszystkie zasady i powód, dla którego nie mają zastosowania. Przykłady obejmują użytkowników i grupy, które mogą być wykluczone z zasad.

## <a name="use-case"></a>Przypadek użycia

Wiele organizacji tworzy zasady oparte na lokalizacjach sieciowych, zezwalając na zaufane lokalizacje i blokując lokalizacje, w których dostęp nie powinien mieć miejsca.

Aby sprawdzić, czy konfiguracja została wykonana odpowiednio, administrator może użyć narzędzia Co jeśli, aby naśladować dostęp, z lokalizacji, która powinna być dozwolona i z lokalizacji, której należy odmówić.

![Co zrobić, jeśli narzędzie pokazujące wyniki z dostępem do bloku](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

W tym przypadku użytkownik będzie zablokowany dostęp do dowolnej aplikacji w chmurze podczas podróży do Korei Północnej, ponieważ firma Contoso zablokowała dostęp z tej lokalizacji.

Ten test można rozszerzyć w celu uwzględnienia innych punktów danych, aby zawęzić zakres.

## <a name="next-steps"></a>Następne kroki

* [Co to jest dostęp warunkowy?](overview.md)
* [Co to jest usługa Azure Active Directory Identity Protection?](../identity-protection/overview-v2.md)
* [Co to jest tożsamość urządzenia?](../devices/overview.md)
* [Jak to działa: usługa Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
