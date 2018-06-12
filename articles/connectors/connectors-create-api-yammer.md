---
title: Dodaj łącznik usługi Yammer w aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie usługi Yammer łącznika z parametrami interfejsu API REST
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 63f7b341b456d51cbde523684275a99632a672ed
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296479"
---
# <a name="get-started-with-the-yammer-connector"></a>Rozpoczynanie pracy z łącznikiem usługi Yammer
Nawiązać Yammer do konwersacji dostęp w sieci przedsiębiorstwa. Za pomocą usługi Yammer można:

* Tworzenie sieci przepływu biznesowe na podstawie danych otrzymywanych z usługi Yammer. 
* Użyj uaktywnia dla istnieje nowy komunikat z grupy lub źródło danych z następujących czynności.
* Akcje Umożliwia opublikowanie wiadomości, Pobierz wszystkie komunikaty i inne. Te akcje uzyskać odpowiedzi, a następnie udostępnić dane wyjściowe do innych działań. Na przykład gdy pojawi się nowy komunikat, możesz wysłać wiadomość e-mail przy użyciu usługi Office 365.

Rozpoczynanie pracy przez tworzenie aplikacji logiki teraz; zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-yammer"></a>Utwórz połączenie z usługi Yammer
Do korzystania z łącznika usługi Yammer, należy najpierw utworzyć **połączenia** następnie podaj szczegóły tych właściwości: 

| Właściwość | Wymagane | Opis |
| --- | --- | --- |
| Token |Yes |Podaj poświadczenia platformy Yammer |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/yammer/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).