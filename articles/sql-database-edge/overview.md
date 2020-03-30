---
title: Co to jest usługa Azure SQL Database Edge? | Microsoft Docs
description: Dowiedz się więcej o usłudze Azure SQL Database Edge
keywords: wprowadzenie do krawędzi bazy danych SQL, co to jest krawędź bazy danych SQL, omówienie krawędzi bazy danych SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246708"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Co to jest usługa Azure SQL Database Edge Preview?

Usługa Azure SQL Database Edge Preview to zoptymalizowany aparat relacyjnej bazy danych przeznaczony dla wdrożeń usługi IoT i usługi IoT Edge. Zapewnia możliwości tworzenia warstwy przechowywania i przetwarzania danych o wysokiej wydajności dla aplikacji i rozwiązań IoT. Usługa Azure SQL Database Edge zapewnia możliwości przesyłania strumieniowego, przetwarzania i analizowania relacyjnych i nierelacyjnych, takich jak JSON, wykres i dane szeregów czasowych, co czyni go właściwym wyborem dla różnych nowoczesnych aplikacji IoT.

Usługa Azure SQL Database Edge jest oparta na najnowszych wersjach aparatu bazy danych programu Microsoft SQL Server Database [Engine,](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json)który zapewnia wiodącą w branży wydajność, zabezpieczenia i możliwości przetwarzania zapytań. Ponieważ usługa Azure SQL Database Edge jest zbudowana na tym samym silniku co SQL Server i Azure SQL Database, zapewnia tę samą powierzchnię programowania T-SQL, która ułatwia i przyspiesza tworzenie aplikacji i rozwiązań, a jednocześnie sprawia, że aplikacja jest łatwiejsza i szybsza, a jednocześnie sprawia, że aplikacja jest łatwiejsza i szybsza, a jednocześnie sprawia, że aplikacja jest łatwiejsza i szybsza, a jednocześnie sprawia, że aplikacja jest łatwiejsza i szybsza, a jednocześnie sprawia, że aplikacja jest między urządzeniami IoT Edge, centrami danych i chmurą prosto do przodu.

## <a name="deployment-models"></a>Modele wdrażania

Usługa Azure SQL Database Edge jest dostępna w portalu Azure Marketplace i może zostać wdrożona jako moduł dla [usługi Azure IoT Edge.](../iot-edge/about-iot-edge.md) Aby uzyskać więcej informacji, zobacz [Wdrażanie usługi Azure SQL Database Edge](deploy-portal.md).<br>

![Diagram przeglądu krawędzi bazy danych SQL](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Wersje usługi SQL Database Edge

Sql Database Edge jest dostępny z trzema różnymi wersjami lub planami oprogramowania. Te wersje mają identyczne zestawy funkcji i różnią się tylko pod względem ich praw użytkowania i ilości procesora / pamięci, które obsługują.

   |**Planowanie**  |**Opis**  |
   |---------|---------|
   |Deweloper usługi Azure SQL Database Edge  |  Rozwój tylko sku, każdy kontener SQL Database Edge jest ograniczony do 4 rdzeni i 32 GB pamięci  |
   |Azure SQL Database Edge    |  Produkcja sku, każdy kontener SQL Database Edge jest ograniczona do maksymalnie 8 rdzeni i 64 GB pamięci. |

## <a name="pricing-and-availability"></a>Ceny i dostępność

Usługa Azure SQL Database jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat cen i dostępności, zobacz [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Aby zrozumieć różnice funkcji między usługą Azure SQL Database Edge i SQL Server, a także różnice między różnymi opcjami usługi Azure SQL Database Edge, zobacz [funkcje bazy danych bazy danych bazy danych bazy danych bazy danych bazy danych bazy danych SQL](https://azure.microsoft.com/services/sql-database-edge/)Database .

## <a name="streaming-capabilities"></a>Możliwości przesyłania strumieniowego  

Usługa Azure SQL Database Edge zapewnia wbudowane funkcje przesyłania strumieniowego do analizy w czasie rzeczywistym i złożonego przetwarzania zdarzeń. Funkcja przesyłania strumieniowego jest zbudowana przy użyciu tych samych konstrukcji co [usługa Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) i zapewnia podobne możliwości jak usługa Azure Stream Analytics w [usłudze IoT Edge.](../stream-analytics/stream-analytics-edge.md)

Aparat przesyłania strumieniowego dla usługi Azure SQL Database Edge został zaprojektowany z myślą o małych opóźnieniach, odporności, efektywnym wykorzystaniu przepustowości i zgodności.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Możliwości uczenia maszynowego i sztucznej inteligencji

Usługa Azure SQL Database Edge zapewnia wbudowane funkcje uczenia maszynowego i analizy, integrując środowisko uruchomieniowe OPEN FORMAT ONNX (Open Neural Network Exchange), które umożliwia wymianę modeli uczenia głębokiego i sieci neuronowych między różnymi strukturami. Aby uzyskać więcej informacji na temat ONNX, zobacz [tutaj](https://onnx.ai/). Środowisko wykonawcze ONNX zapewnia elastyczność tworzenia modeli w wybranym języku lub narzędziach, które następnie można przekonwertować na format ONNX do wykonania w ramach usługi SQL Database Edge. Aby uzyskać więcej informacji, zobacz [Uczenie maszynowe i sztuczna inteligencja z ONNX w programie SQL Database Edge](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Praca z usługą Azure SQL Database Edge

Usługa Azure SQL Database Edge ułatwia tworzenie i utrzymywanie aplikacji, co jest łatwiejsze i bardziej wydajne. Użytkownicy mogą korzystać ze wszystkich znanych narzędzi i umiejętności do tworzenia doskonałych aplikacji i rozwiązań dla swoich potrzeb usługi IoT Edge. Użytkownik może tworzyć w programie SQL Database Edge przy użyciu następujących narzędzi:

- [Portal Azure](https://portal.azure.com/) — aplikacja oparta na sieci Web do zarządzania wszystkimi usługami platformy Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) — bezpłatna aplikacja kliencka do pobrania do zarządzania dowolną infrastrukturą SQL, od programu SQL Server do bazy danych SQL.
- [SQL Server Data Tools w programie Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) — bezpłatna, możliwa do pobrania aplikacja kliencka do tworzenia relacyjnych baz danych programu SQL Server, baz danych SQL, pakietów Integration Services, modeli danych usług Analysis Services i raportów reporting services.
- [Usługa Azure Data Studio](/sql/azure-data-studio/what-is/) — bezpłatne, możliwe do pobrania narzędzie do wieloplatformowej bazy danych dla specjalistów w zakresie danych przy użyciu lokalnych i chmurowych platform danych z rodziny microsoft w systemach Windows, MacOS i Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) — darmowy, do pobrania, edytor kodu open source dla systemów Windows, macOS i Linux. Obsługuje rozszerzenia, w tym [rozszerzenie mssql](https://aka.ms/mssql-marketplace) do wykonywania zapytań microsoft SQL Server, Azure SQL Database i Usługi Azure SQL Data Warehouse.


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o cenach i dostępności, zobacz [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Żądanie włączenia usługi Azure SQL Database Edge dla subskrypcji.
- Aby rozpocząć, zobacz następujące elementy:
  - [Wdrażanie usługi SQL Database Edge w witrynie Azure portal](deploy-portal.md)
  - [Uczenie maszynowe i sztuczna inteligencja z bazą danych SQL Edge](onnx-overview.md)
