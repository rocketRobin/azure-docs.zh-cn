---
title: "教程：Azure Active Directory 与 TextMagic 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 TextMagic 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: jeedes
ms.openlocfilehash: 701a97aa2001256354fe487c9499ff3cbb8b4540
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>教程：Azure Active Directory 与 TextMagic 的集成

本教程介绍了如何将 TextMagic 与 Azure Active Directory (Azure AD) 进行集成。

将 TextMagic 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 TextMagic。
- 可以让用户使用其 Azure AD 帐户自动登录到 TextMagic（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 TextMagic 的集成，需要以下项：

- 一个 Azure AD 订阅
- 一个启用了单一登录的 TextMagic 订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 TextMagic
2. 配置并测试 Azure AD 单一登录

## <a name="adding-textmagic-from-the-gallery"></a>从库中添加 TextMagic
若要配置 TextMagic 与 Azure AD 的集成，需要从库中将 TextMagic 添加到托管 SaaS 应用列表。

**若要从库中添加 TextMagic，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入“TextMagic”，在结果面板中选择“TextMagic”，然后单击“添加”按钮添加应用程序。

    ![结果列表中的 TextMagic](./media/active-directory-saas-textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户配置和测试 TextMagic 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 TextMagic 用户。 换句话说，需要在 Azure AD 用户与 TextMagic 中的相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值指定为 TextMagic 中“用户名”的值来建立此链接关系。

若要配置和测试 TextMagic 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 TextMagic 测试用户](#create-a-textmagic-test-user)** - 在 TextMagic 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 TextMagic 应用程序中配置单一登录。

**若要配置 TextMagic 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **TextMagic** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-textmagic-tutorial/tutorial_textmagic_samlbase.png)

3. 在“TextMagic 域和 URL”部分中，如果要在 **IDP** 发起的模式下配置应用程序，请执行以下步骤：

    ![TextMagic 域和 URL 单一登录信息](./media/active-directory-saas-textmagic-tutorial/tutorial_textmagic_url.png)

    在“标识符”文本框中，键入一个 URL：`https://my.textmagic.com/saml/metadata`

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![TextMagic 域和 URL 单一登录信息](./media/active-directory-saas-textmagic-tutorial/url1.png)

    在“登录 URL”文本框中，键入 URL `https://my.textmagic.com/login/sso`


5. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-textmagic-tutorial/tutorial_textmagic_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-textmagic-tutorial/tutorial_general_400.png)
    
7. 在“TextMagic 配置”部分中，单击“配置 TextMagic”以打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![TextMagic 配置](./media/active-directory-saas-textmagic-tutorial/tutorial_textmagic_configure.png) 

8. 在另一个 Web 浏览器窗口中，以管理员身份登录到 TextMagic 公司站点。

9. 在用户名下选择“帐户设置”。

    ![TextMagic 配置](./media/active-directory-saas-textmagic-tutorial/config1.png) 
10. 单击“单一登录 (SSO)”选项卡并填写以下字段：  
    
    ![TextMagic 配置](./media/active-directory-saas-textmagic-tutorial/config2.png)

    a. 在“标识提供者实体 ID”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。

    b. 在“标识提供者 SSO URL:”文本框中，粘贴从 Azure 门户复制的“单一登录服务 URL”的值。

    c. 在“标识提供者 SLO URL:”文本框中，粘贴从 Azure 门户复制的“注销 URL”的值。

    d. 在记事本中打开从 Azure 门户下载的 base-64 编码证书，将其内容复制到剪贴板，然后粘贴到“公用 x509 证书:”文本框。

    e. 单击“保存” 。


> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-textmagic-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-textmagic-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-textmagic-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-textmagic-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建” 。
 
### <a name="create-a-textmagic-test-user"></a>创建一个 TextMagic 测试用户

应用程序支持实时用户预配，且进行身份验证后，会在应用程序中自动创建用户。 你需要在首次登录时填写一次该信息以在系统中激活子帐户。
此部分不存在任何操作项。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 TextMagic 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 TextMagic，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“TextMagic”。

    ![应用程序列表中的 TextMagic 链接](./media/active-directory-saas-textmagic-tutorial/tutorial_textmagic_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 TextMagic 磁贴时，应该会自动登录到 TextMagic 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_203.png

