---
title: Widok SAML zwrócony przez usługę kontroli dostępu (Java)
description: Dowiedz się, jak wyświetlić SAML zwracany przez usługi kontroli dostępu w aplikacji Java hostowanej na platformie Azure.
services: active-directory
documentationcenter: java
author: rmcmurray
manager: mbaldwin
ms.assetid: 6cd216f9-eb43-46b4-b30d-f194d0ae2d48
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: f6cdb2877541bd584f881b9dfeff38500d19ab82
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155562"
---
# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>Jak wyświetlić SAML zwrócona przez usługę Azure kontroli dostępu
W tym przewodniku opisano sposób wyświetlania podstawowej Assertion Markup języka SAML (Security) Powrót do aplikacji przez usługi kontroli dostępu (ACS) platformy Azure. Przewodnik opiera się na [jak uwierzytelniać użytkowników sieci Web z Eclipse przy użyciu usługi kontroli dostępu platformy Azure](active-directory-java-authenticate-users-access-control-eclipse.md) tematu, podając kod, który zawiera informacje dotyczące SAML. Ukończona aplikacja będzie wyglądać podobnie do następującego.

![Przykładowe dane wyjściowe SAML][saml_output]

Aby uzyskać więcej informacji dotyczących usług ACS, zobacz [następne kroki](#next_steps) sekcji.

> [!NOTE]
> Filtr kontroli usługi dostępu do usługi Azure jest podgląd technologii społeczności. Jako wydaniu wstępnym oprogramowania go nie jest oficjalnie obsługiwana przez firmę Microsoft.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać zadania w tym przewodniku, należy wykonać próbki w [jak uwierzytelniać użytkowników sieci Web z Eclipse przy użyciu usługi kontroli dostępu platformy Azure](active-directory-java-authenticate-users-access-control-eclipse.md) i używać go jako punktu wyjścia do celów tego samouczka.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>Dodaj bibliotekę JspWriter do kompilacji zestawu ścieżkę i wdrożenia
Dodaj bibliotekę, która zawiera **javax.servlet.jsp.JspWriter** klasy do kompilacji zestawu ścieżkę i wdrożenia. Jeśli używasz Tomcat biblioteka jest **jsp api.jar**, który znajduje się w Apache **lib** folderu.

1. W obszarze Eksplorator projektów w środowisku Eclipse, kliknij prawym przyciskiem myszy **MyACSHelloWorld**, kliknij przycisk **ścieżkę kompilacji**, kliknij przycisk **Konfiguruj ścieżkę kompilacji**, kliknij przycisk **biblioteki**, a następnie kliknij pozycję **Dodaj zewnętrzne JARs**.
2. W **JAR wybór** okno dialogowe, przejdź do niezbędne JAR, zaznacz go, a następnie kliknij **Otwórz**.
3. Z **właściwości MyACSHelloWorld** nadal otwarty, kliknij przycisk okna dialogowego **wdrożenia zestawu**.
4. W **zestawu wdrażania Web** okna dialogowego, kliknij przycisk **Dodaj**.
5. W **nowe dyrektywa zestawu** okna dialogowego, kliknij przycisk **wpisy ścieżka kompilacji języka Java** , a następnie kliknij przycisk **dalej**.
6. Wybierz odpowiednią bibliotekę i kliknij przycisk **Zakończ**.
7. Kliknij przycisk **OK** zamknąć **właściwości MyACSHelloWorld** okna dialogowego.

## <a name="modify-the-jsp-file-to-display-saml"></a>Zmodyfikuj plik JSP, aby wyświetlić SAML
Modyfikowanie **index.jsp** można użyć poniższego kodu.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {

            try
            {
                String nodeName;
                int nChild, i;

                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");

                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }

                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                    {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    

                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                        for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);

                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }

                                     }
                                     out.println("<br>");

                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>

        <%
        try
        {
            String data  = (String) request.getAttribute("ACSSAML");

            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();

            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA);
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();

            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();

            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e)
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }

        %>
    </body>
    </html>

## <a name="run-the-application"></a>Uruchamianie aplikacji
1. Uruchom aplikację w emulatorze komputera lub wdrożyć na platformie Azure, wykonując kroki opisane w [jak uwierzytelniać użytkowników sieci Web z Eclipse przy użyciu usługi kontroli dostępu platformy Azure](active-directory-java-authenticate-users-access-control-eclipse.md).
2. Uruchom przeglądarkę i Otwórz aplikację sieci web. Po zalogowaniu się do aplikacji zostanie wyświetlone informacje SAML, w tym potwierdzenia zabezpieczenia udostępniane przez dostawcę tożsamości.

## <a name="next-steps"></a>Kolejne kroki
Aby jeszcze bardziej Eksploruj funkcje usług ACS i eksperymentować bardziej zaawansowanych scenariuszy, zobacz [2.0 usługi kontroli dostępu][Access Control Service 2.0].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[How to Authenticate Web Users with Azure Access Control Service Using Eclipse]: active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
