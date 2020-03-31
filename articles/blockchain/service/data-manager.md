---
title: Co to jest Blockchain Data Manager dla usługi Azure Blockchain
description: Blockchain Data Manager do przechwytywania, przekształca i dostarcza dane blockchain do tematów event grid.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209447"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Co to jest menedżer danych łańcucha bloków dla usługi Azure Blockchain Service?

Blockchain Data Manager przechwytuje, przekształca i dostarcza dane transakcji usługi Azure Blockchain Service do tematów usługi Azure Event Grid, zapewniając niezawodną i skalowalną integrację księgi blockchain z usługami platformy Azure.

W większości scenariuszy łańcucha bloków dla przedsiębiorstw księga blockchain jest częścią rozwiązania. Na przykład, aby przenieść środek trwały z jednej jednostki do innej, potrzebny jest mechanizm przesyłania transakcji. Następnie potrzebny jest mechanizm odczytywania danych księgi, aby upewnić się, że transakcja wystąpiła, została zaakceptowana, a wynikowe zmiany stanu są następnie zintegrowane z rozwiązaniem end-to-end. W tym przykładzie jeśli piszesz inteligentną umowę w celu przeniesienia zasobów, możesz użyć Blockchain Data Manager do integracji aplikacji poza łańcuchem i magazynów danych. W przykładzie przeniesienia zasobów, gdy zasób jest przenoszony w łańcuchu bloków, zdarzenia i zmiany stanu nieruchomości są dostarczane przez Blockchain Data Manager za pośrednictwem usługi Event Grid. Następnie można użyć wielu możliwych programów obsługi zdarzeń dla usługi Event Grid do przechowywania danych łańcucha bloków poza łańcuchem lub reagowania na zmiany stanu w czasie rzeczywistym.

Blockchain Data Manager pełni trzy główne funkcje: przechwytywanie, przekształcanie i dostarczanie.

![Funkcje Blockchain Data Manager](./media/data-manager/functions.png)

## <a name="capture"></a>Przechwytywanie

Każde wystąpienie programu Blockchain Data Manager łączy się z jednym węzłem transakcji członkowskiej usługi Azure Blockchain Service. Tylko użytkownicy z dostępem do węzła transakcji można utworzyć połączenie zapewniające właściwą kontrolę dostępu do danych klienta. Wystąpienie programu Blockchain Data Manager niezawodnie przechwytuje wszystkie nieprzetworzone dane transakcji blokowych i nieprzetworzonych z węzła transakcji i może być skalowane w celu obsługi obciążeń przedsiębiorstwa.

## <a name="transform"></a>Przekształcanie

Za pomocą Blockchain Data Manager można dekodować stan zdarzeń i właściwości, konfigurując inteligentne aplikacje kontraktowe w Blockchain Data Manager. Aby dodać umowę inteligentną, należy podać umowę ABI i kod bajtowy. Blockchain Data Manager używa inteligentnych artefaktów kontraktu do dekodowania i odnajdowania adresów umów. Po dodaniu aplikacji blockchain do wystąpienia Blockchain Data Manager dynamicznie odnajduje adres inteligentnego kontraktu, gdy inteligentny kontrakt zostanie wdrożony do konsorcjum i wysyła zdekodowany stan zdarzenia i właściwości do skonfigurowanych miejsc docelowych.

## <a name="deliver"></a>Dostarczanie

Blockchain Data Manager obsługuje wiele połączeń wychodzących w temacie siatki zdarzeń dla danego wystąpienia Menedżera danych Blockchain. Możesz wysyłać dane łańcucha bloków do jednego miejsca docelowego lub wysyłać dane łańcucha bloków do wielu miejsc docelowych. Korzystając z Menedżera danych blockchain, możesz utworzyć skalowalne rozwiązanie do publikowania danych oparte na zdarzeniach dla każdego wdrożenia łańcucha bloków.

## <a name="configuration-options"></a>Opcje konfiguracji

Blockchain Data Manager można skonfigurować tak, aby spełniał potrzeby twojego rozwiązania. Na przykład można aprowizować:

* Pojedyncze wystąpienie menedżera danych blockchain dla członka usługi Azure Blockchain.
* Wystąpienie menedżera danych blockchain na węzeł transakcji usługi Azure Blockchain Service. Na przykład węzły transakcji prywatnych mogą mieć własne wystąpienie Menedżera danych Blockchain w celu zachowania poufności.
* Wystąpienie Menedżera danych blockchain może obsługiwać wiele połączeń wyjściowych. Jedno wystąpienie programu Blockchain Data Manager może służyć do zarządzania wszystkimi punktami integracji publikowania danych dla członka usługi Azure Blockchain Service.

## <a name="next-steps"></a>Następne kroki

Spróbuj [utworzyć wystąpienie menedżera danych blockchain](data-manager-portal.md) dla członka usługi Azure Blockchain.
