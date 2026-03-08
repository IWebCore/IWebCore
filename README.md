# IWebCore

<p align="center">
<pre> 
 _____  _    _        _      _____
|_   _|| |  | |      | |    /  __ \
  | |  | |  | |  ___ | |__  | /  \/  ___   _ __  ___
  | |  | |/\| | / _ \| '_ \ | |     / _ \ | '__|/ _ \
 _| |_ \  /\  /|  __/| |_) || \__/\| (_) || |  |  __/
 \___/  \/  \/  \___||_.__/  \____/ \___/ |_|   \___|
</pre>
</p>

## 📦 Core Module Introduction

### 🎯 IMakeCore - Cpper's Package Management Tool
**Simplest Cross-Platform Package Management Tool**

IMakeCore builds upon existing foundations, providing a source-based package management tool. It supports cross-platform, non-invasive integration with qmake/cmake. IMakeCore can be integrated with just one command, and packages can be referenced in the following way:

```json
{
    "packages":{
        "ICore":"*",
        "ITcp":"*",
        "IHttp":"1.0.0",
        "IRdb":"1.0.0",
        "INody":"1.0.0",
        "asio":"*",
        "nlohmann.json": "*",
        "zlib":"1.3.1",
        "stachenov.quazip":"1.5.0"
    }
}
```

[View Documentation →](https://docs.iwebcore.org/IMakeCore/quick_start)

---

### ⚠️ IHttpCore - Reflective/Annotation-based HTTP Framework
**High-Performance User-Friendly HTTP Server Framework**

IHttpCore is a lightweight but powerful HTTP service framework. Users can complete HTTP request-response using annotations.

```cpp
class PackageController : public IHttpControllerInterface<PackageController>
{
    Q_GADGET
    $AsController(package)
public:
    PackageController();

    $GetMapping(updatePackage)
    IStatusResponse updatePackage();

    $GetMapping(pagingList, /list)
    IJsonResponse pagingList(int $Query(page), int $Query(per_page), QString $Query(sort), QString $Query(order));

    $GetMapping(versions, /versions/<name>)
    QList<PackageInfo> versions(QString $Path(name));
}
```

[Quick Start →](https://docs.iwebcore.org/IHttpCore/overview)

---

### ✅ IRdbCore - Relational Database
**Modern Dumb Database Operation Tool**

#### User.h
```cpp
class User : public IRdbTableInterface<User>
{
    Q_GADGET
public:
    User();

public:
    $AutoIncrement(id)
    $PrimaryKey(id)
    $ColumnDeclare(std::int64_t, id)
    std::int64_t id{0};

    $Column(QString, user_name)

    $Column(QString, email)

    $Column(QString, password_hash)

    $Column(QString, salt)
};
```

#### Model.h
```cpp
class UserModel : public IRdbTableModelInterface<UserModel, User, PkgDatabase>
{
public:
    UserModel();
};
```

#### Usage.h
```cpp
UserModel m_userModel;

// check email and user name
if(m_userModel.exist(IRdb::whereEqual(User::$field_email, email))){
    return IHttpBadRequestInvalid("email has be used, please select another email, or login");
}
if(m_userModel.exist(IRdbCondition().whereEqual(User::$field_user_name, username))){
    return IHttpBadRequestInvalid("user name already exist");
}

// insert user
User user;
user.user_name = username;
user.email = email;
user.salt = xxxx;
user.password_hash = xxxxx;
m_userModel.insertOneRef(user);

// find user by id
auto user = m_userModel.findById(id);
```

[Learn More →](https://docs.iwebcore.org/IRdbCore/overview)

---

### 💻 ICmd - Command-Line Development Suite
**User-Friendly Command-Line Parsing, Development Framework**

ICmd is a command-line development framework based on ICore, providing command-line parsing, command-line arguments, command-line options, command-line subcommands, etc., allowing users to focus on functional code.

#### UserSetEmail.h
```cpp
#pragma once

#include "cmd/ICmdInterface.h"

class UserSetEmail : public ICmdInterface<UserSetEmail>
{
    Q_GADGET
    $AsCmd(user)
public:
    UserSetEmail();

    $CmdArgs(QString, email)
    $CmdArgsPostHandle(email, emailPostHandle)
    void emailPostHandle();

public:
    $CmdMappingMemo(setEmail, "set email info")
    $CmdMapping(setEmail, email, set)
    void setEmail();
};
```

#### UserSetEmail.cpp
```cpp
#include "UserSetEmail.h"
#include "core/util/IFileUtil.h"
#include "data/Env.h"

UserSetEmail::UserSetEmail()
{

}

void UserSetEmail::emailPostHandle()
{
    if(!Env::isValidEmail(email)){
        qDebug().noquote().nospace() << "email is not valid";
        quick_exit(1);
    }
}

void UserSetEmail::setEmail()
{
    auto path = Env::instance().imakeRoot() + "/.data/.INFO";
    IJson json = IJson::object();
    if(QFileInfo(path).exists()){
        auto content = IFileUtil::readFileAsString2(path);
        if(IJson::accept(content.toStdString())){
            json = IJson::parse(content.toStdString());
        }
    }

    json["email"] = email.toStdString();
    IFileUtil::writeToFile(path, QString::fromStdString(json.dump(4)));
    quick_exit(0);
}
```

[Get Started →](https://docs.iwebcore.org/ICmd/overview)

---

## 🚀 Getting Started

To get started with IWebCore, check out the [documentation](https://docs.iwebcore.org) or explore the individual module guides above.


## 📞 Support

For questions and support, please open an issue on [GitHub](https://github.com/IWebCore/IWebCore/issues).