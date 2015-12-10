# 浅析Android打包

使用Android Studio的"build"选项下面的"Generate Signed APK..."选项，我们可以很轻松地打包出一个安装文件。但在现在的商业应用中，企业不会只打包出一个APK包，然后提供给大家下载。企业同时还需要要其他不同的渠道上发布APP，国内的主流渠道就有豌豆荚、应用宝、小米应用商店等等数十个渠道，通常每个渠道的APK包都会有一点差异，那么我们会每打包一个渠道的APK，就去改信息，然后执行一遍"Generate Signed APK..."吗？

Navie！当然不会。使用gradle打包apk已经成为当前主流趋势