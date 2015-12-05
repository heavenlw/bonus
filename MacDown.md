# A HKBU event registration system

This event registration system allow students in HKBU to sign up an personal account and register different events from different department.

This system has two parts:App and Web.

Both of Them provide those functions:

**1.login/logout**
**2.register an event**
**3.unregister an event**
**4.show registered events of a logged-in user.**

## User account in this system

Here I provide 3 different type of users

**super administrator**  

username:admin    password:123456

**administrator from different department**

username:martin   password:123456

**student user**

username:lw   password:123456    or you can register your own account


# Bonus achieved in this system

## Bonus on web

### 1. Different navigation bar based on the login-status

####Guest Mode
![](
)
####Admin Mode

####User Mode

To achieve this, I use js in layout.ejs and add the different account type into session in login.js.

Actually, different accounts have different type:

In bootstrap.js, I add different **type** into different account.

As super admin, it's type is **"1"**.

As normal admin, it's type is **"2"** and it's department is "comp".

As student user, it's type is **"3"** and when a student register an account, this account's type will be set as **"3"** automatically by **regiester** function 

Here is the code in **register function at Usercontroller**

~~~
register: function(req, res) {

        if (req.method == "POST") {
            req.body.User.Type = "3";

            User.create(req.body.User).exec( function(err, model) {

                return res.send("Successfully Created!");

            });
        }

        else {

            return res.view('user/register');
        }
    }
~~~

Here is the code in **bootstrap.js**

~~~
var user = {"username": "admin", "password":"123456", "id":1,"type":"1"}

    User.create(user).exec( function (err, model)  {});

    user = {"username": "martin", "password":"123456", "id":2,"type":"2","org":"comp"}

    User.create(user).exec( function (err, model)  {});	

     user = {"username": "lw", "password":"123456", "id":3,"type":"3"}
      User.create(user).exec( function (err, model)  {}); 
~~~


Here is the code in **layout.ejs**

~~~
<script>
function remove (id) {  
	var idObject = document.getElementById(id);  
	if (idObject != null)  
	idObject.parentNode.removeChild(idObject);  
}  

if("<%=req.session.username%>" !="null"&&"<%=req.session.username%>"!="undefined")
	{
		remove("login");
		if("<%=req.session.usertype%>" =="3")   //when user login,its' account type will be recorded into ression
			{
				remove("create");
                    
				remove("admin");
			}
	}
else
	{
		remove("create");
		remove("myreg");
		remove("admin");
		remove("logout");
	}
</script>
~~~
Here is the code in **login.js at Usercontroller**

~~~
	login: function (req, res) {

        if (req.method == "GET")
            return res.view('user/login');
        else {

            sails.log(req.body);
            sails.log(req.body.username);
			  User.findOne({username:req.body.username})
            .exec( function (err, user) {

                if (user == null) 
                    return res.send("No such user");
                
                if (user.password != req.body.password) 
                    return res.send("Wrong Password");
                    req.session.username = req.body.username;  
                    req.session.user_id = user.id;   
                    req.session.usertype = user.type;
                    req.session.org = user.org;
                  if(req.body.type=="1")
                {  
                    return res.json(user);
                }
                else
                {
                    return res.redirect("BuAdmin/index");
                }
            })
            
        }
    }
~~~

###2. Different status in event detail page

####Guest Mode
![](
)

####User Mode when the event had been regiestered 
![](
)

To achieve this, I add js in detail.ejs

~~~
<div style="min-height:50;max-height:135px;overflow:hidden;">
<a id="reg"  href="/buadmin/addBeReg/<%=model.id%>">Regiester</a>
</div><br>
<div  style="min-height:50;max-height:135px;overflow:hidden;">
<a id="login" href="/user/login">Want To Register this Event? Just Login Right Now.</a>
					<script>
				function remove (id) {  
					var idObject = document.getElementById(id);  
					if (idObject != null)  
						idObject.parentNode.removeChild(idObject);  
				}
				if("<%=req.session.username%>" =="null"||"<%=req.session.username%>"=="undefined")
				{
					remove("reg");
				}
				else
				{
					remove("login");
				}

				</script>
				</div><br>
~~~



