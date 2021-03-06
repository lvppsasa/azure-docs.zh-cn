要在应用程序上启用登录，需要创建登录策略。 此策略描述了使用者在登录过程中将要体验的内容以及应用程序在成功登录时会接收到的令牌内容。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)] 单击“登录策略”。

单击边栏选项卡顶部的“ **+添加** ”。

“名称”确定应用程序使用的登录策略名称。 例如，输入 **SiIn**。

单击“标识提供者”，并选择“本地帐户登录”。 或者，也可以选择社交标识提供者（如果已配置）。 单击 **“确定”**。

单击“应用程序声明”。 在这里，可以选择希望在成功登录体验后发回到应用程序的令牌中返回的声明。 例如，选择“显示名称”、“标识提供者”、“邮政编码”和“用户的对象 ID”。 单击 **“确定”**。

单击“创建” 。 请注意，刚刚创建的策略在“登录策略”边栏选项卡中显示为“B2C_1_SiIn”（自动添加 **B2C\_1\_**片段）。

通过单击“B2C_1_SiIn”打开策略。

在“应用程序”下拉列表中选择“Contoso B2C 应用”，并在“回复 URL/重定向 URI”下拉列表中选择 `https://localhost:44321/`。

单击“立即运行”。 将打开一个新的浏览器选项卡，可以运行登录到应用程序的使用者体验。

> [!NOTE]
> 策略创建和更新最多需要一分钟才能生效。
>