Quickstart
**********

After installing a clean Cohesion project you should modify your composer.json file and set the project name etc for your project.

You main application code will go into the ``src/`` directory. You should create separate subdirectories within that folder for each of your services.

All front end code goes within the ``www/`` directory. With your controllers in ``www/controllers``, views in ``www/veiws`` and templates in ``www/templates``.


Frontend
========

If you haven't changed your configuration yet your default controller should be 'Home' with the default function on 'index'. This means that if you go to http://localhost/ it should look for the ``HomeController`` and execute the ``index`` function on it.

The Cohesion Framework comes with a very simple home page. Open up ``www/controllers/HomeController.php`` you should see it looks like this:

.. code-block:: php

    use \Cohesion\Structure\Controller;
    use \Cohesion\Structure\Factory\ViewFactory;

    class HomeController extends Controller {

        public function index() {
            $view = ViewFactory::createView('Home');
            return $view->generateView();
        }
    }

Here we can see it's creating a 'Home' view. This can be found at ``www/views/HomeView.php``.

.. code-block:: php

    class HomeView extends MyView {
        public function __construct($template, $engine, $vars) {
            parent::__construct($template, $engine, $vars);
            $vars['page'] = 'home';
            $vars['title'] = $vars['site_name'] . ' Home';
            $this->addVars($vars);
        }
    }
    
As you can see this is extending ``MyView`` class. All your views should extend that class (feel free to rename it) and that is where you can put any common logic that all your views should have access to.

Next we see it's calling it's parent constructor and passing through the template engine and variables. Don't worry too much about this, this is just setting up the templating engine.

The last thing in this file we see is that it's adding a new variable, 'page' and setting it to 'home'. You can add as many variables as you want here and they will all be available within the front end template. **page** is a special variable though and must be set in every view. The variable set in ``page`` is used to decide which template to use. In this case it will be using the ``home.html`` template. ``title`` is the variable that will be used for the title of this page. You can see here it's using one of the default variables set in the configuration 'site_name'.

Let's look at the root template ``www/templates/index.html``. All pages use the same template by default. It's possible to override this behaviour in the ``View`` class but most of the time you'll probably want all your pages to have the same header and footer.

.. code-block:: html

    <!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="utf-8" />
            <title>{{title}}</title>
        </head>
        <body>
            {{> header}}
            {{> page}}
            {{> footer}}
        </body>
    </html>

As you can see this template is extremely simple and you'll probably want to fill it out much more. Here you can start to see the Mustache templating library in action. ``{{title}}`` will be replaced with the ``title`` set in the view.

In Mustache the ``>`` modifier is used to load a sub template (partial). In Cohesion the partial template name can be overwritten by setting a variable with that name. Since we set the variable ``page`` to ``home`` it will load the ``www/templates/home.html`` template in the middle. But the header and footer weren't overwritten so they will just use the templates ``www/templates/header.html`` and ``www/templates/footer.html``. So if you modify the header template it will update the header on every page then we can just change the ``page`` variable to set the main content of the page.


Backend / Services
==================

Your main code for all your business logic will go into folders within the ``src/`` directory.

Let's run through creating a user.

We'll start by creating a directory ``src/user/`` and adding a ``User.php`` file to it as our DTO:

.. code-block:: php

    use \Cohesion\Structure\DTO;

    class User extends DTO {
        protected $id;
        protected $username;
        protected $email;
        protected $password;

        const MIN_USERNAME_LENGTH = 3;
        const MAX_USERNAME_LENGTH = 30;
        const MIN_PASSWORD_LENGTH = 6;

        public function setUsername($username) {
            if (strlen($username) < self::MIN_USERNAME_LENGTH) {
                throw new UserSafeException('Username must be at least ' . self::MIN_USERNAME_LENGTH . ' characters long.');
            } else if (strlen($username) > self::MAX_USERNAME_LENGTH) {
                throw new UserSafeException('Username cannot be longer than ' . self::MAX_USERNAME_LENGTH . ' characters.');
            }
            $this->username = $username;
        }

        public function setEmail($email) {
            if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
                throw new UserSafeException('Invalid email address');
            }
            $this->email = $email;
        }

        public function getUsername() {
            return $this->username;
        }

        public function getEmail() {
            return $this->email;
        }
    }

That's all that's needed for now. The constructor of the parent ``DTO`` class will be able to take in an associative array and call the appropriate mutator functions. And also provides a ``getVars()`` function that will return the protected variables as an associative array.

Next lets create a ``UserService`` at ``src/user/UserService.php``. The service is the external API for everything other components need to do with "users". Normally it would include a bunch of authorization code etc but for now let's just keep it simple.

.. code-block:: php

    use \Cohesion\Structure\Service;
    use \Cohesion\Auth\AuthException;

    class UserService extends Service {
        public function getUser($id) {
            return $this->dao->getUser($id);
        }

        public function getUserByUsername($username) {
            return $this->dao->getUserFromUsername($username);
        }

        public function createUser($user) {
            $user = $this->dao->getUserFromUsername($user->getUsername());
            if ($user) {
                throw new AuthException("Username {$user->getUsername()} is already taken");
            }
            try {
                $this->dao->createUser($user);
            } catch (DBException $e) {
                throw new AuthException('Unable to register new user');
            }
            return $user;
        }
    }

The constructor of the parent ``Service`` will create the ``dao`` for us and will look for a class named ``UserDAO`` so let's create that now. ``src/user/UserDAO.php``:

.. code-block:: php

    use \Cohesion\Structure\DAO;

    class UserDAO extends DAO {

        public function getUser($id) {
            $result = $this->db->query('
                SELECT u.id, username, email
                FROM users
                WHERE id = {{id}}
                ', array('id' => $id));
            if ($row = $result->nextRow()) {
                return new User($row);
            } else {
                return null;
            }
        }

        public function getUserFromUsername($username) {
            $result = $this->db->query('
                SELECT u.id, username, email
                FROM users
                WHERE username = {{username}}
                ', array('username' => $username));
            if ($row = $result->nextRow()) {
                return new User($row);
            } else {
                return null;
            }
        }

        public function createUser(&$user) {
            $result = $this->db->query('
                INSERT INTO users
                (username, email, password)
                VALUES
                ({{username}}, {{email}}, {{password}})
                ', $user->getVars());
            $user->setId($result->insertId());
            return $user;
        }
    }

The parent ``DAO`` constructor will set the ``db`` for us so we don't need to worry about getting the database connection. If we wanted to be able to access other libraries from within the ``DAO`` we can override the constructor and define the extra data accesses we need.

For example if we wanted to use a cache we could add a constructor like this:

.. code-block:: php

    use \Cohesion\Structure\DAO;
    use \Cohesion\DataAccess\Database\Database;
    use \Cohesion\DataAccess\Cache\Cache;
    
    class UserDAO extends DAO {

        protected $cache;

        public function __construct(Database $db, Cache $cache) {
            parent::__construct($db);
            $this->cache = $cache;
        }

        public function getUser($id) {
            $userData = $this->cache->get("user_$id");
            if (!$userData) {
                $result = $this->db->query('
                    SELECT u.id, username, email
                    FROM users
                    WHERE id = {{id}}
                    ', array('id' => $id));
                if ($userData = $result->nextRow()) {
                    $this->cache->save("user_$id", $userData);
                }
            }
            if ($userData) {
                return new User($userData);
            } else {
                return null;
            }
        }
        // ...
    }

We don't need to change any code anywhere else other than making sure that the Cache is set up properly in the configuration files.

