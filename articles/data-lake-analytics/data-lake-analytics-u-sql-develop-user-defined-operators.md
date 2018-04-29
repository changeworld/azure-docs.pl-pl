---
title: Opracowywanie operatorów języka U-SQL zdefiniowane przez użytkownika (udo) | Dokumentacja firmy Microsoft
description: 'Dowiedz się, jak opracowywanie operatorów zdefiniowanych przez użytkownika używanych i użyć ponownie w zadań usługi Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: kfile
editor: cgronlun
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: c0164d9f1e40778d3ce0dc7e947f6d2807234468
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Opracowywanie operatorów języka U-SQL zdefiniowane przez użytkownika (udo)
Dowiedz się, jak opracowywanie operatorów zdefiniowanych przez użytkownika do przetwarzania danych w ramach zadania skryptu U-SQL.

Aby uzyskać instrukcje na temat tworzenia ogólnego przeznaczenia zestawy dla U-SQL, zobacz [zestawy opracowanie U-SQL dla zadania usługi Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md)

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definiowanie i użycie operatora zdefiniowanej przez użytkownika w języku U-SQL
**Aby utworzyć i przesłać zadanie U-SQL**

1. Z programu Visual Studio wybierz opcję **Plik > Nowy > Projekt > Projekt U-SQL**.
2. Kliknij przycisk **OK**. Visual Studio tworzy rozwiązanie z plikiem Script.usql.
3. Z **Eksploratora rozwiązań**, rozwiń Script.usql, a następnie kliknij dwukrotnie ikonę **Script.usql.cs**.
4. Wklej następujący kod do pliku:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Suisse", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. Otwórz **Script.usql**i wklej poniższy skrypt U-SQL:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. Określ konto usługi Data Lake Analytics, bazę danych i schemat.
8. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Build Script** (Kompiluj skrypt).
9. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Submit Script** (Prześlij skrypt).
10. Jeśli użytkownik nie nawiązywano połączenia z subskrypcją platformy Azure, pojawi się monit o podanie poświadczeń konta platformy Azure.
11. Kliknij przycisk **przesłać**. Wyniki przesyłania i link do zadania są dostępne w oknie wyników po zakończeniu przekazywania.
12. Kliknij przycisk **Odśwież** przycisk, aby zobaczyć najnowsze stan zadania i odświeżyć ekran.

**Aby wyświetlić dane wyjściowe**

1. Z **Eksploratora serwera**, rozwiń węzeł **Azure**, rozwiń węzeł **usługi Data Lake Analytics**, rozwiń konto usługi Data Lake Analytics, rozwiń węzeł **kont magazynu**, kliknij prawym przyciskiem myszy domyślne magazynu, a następnie kliknij przycisk **Explorer**.
2. Rozwiń węzeł próbek, rozwiń dane wyjściowe, a następnie kliknij dwukrotnie ikonę **Drivers.csv**.

## <a name="see-also"></a>Zobacz także
* [Rozszerzanie wyrażenia języka U-SQL z kodu użytkownika](https://msdn.microsoft.com/library/azure/mt621316.aspx)
* [Użyj narzędzi Data Lake Tools dla programu Visual Studio do tworzenia aplikacji U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
