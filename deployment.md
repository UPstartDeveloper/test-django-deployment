# How to Successfully Deploy a Django Project, on the FIRST PUSH

## Introduction

Being able to deploy a project is one of the signs of a truly great developer in-the-making. If you master this skill, it will help you breathe easier and perform better in all environments in which you build software products - e.g. hackathons, your hobby projects, the Intensives at [Make School](https://www.makeschool.com), and perhaps even your full-time developer job!

This is the process that has worked for me so far, in pushing all my individual as well as team projects made in Django into production. I used Heroku for all the deployments.

My **recommendation** is to follow this step **right at the beginning of your project!** Deployment is one of those steps that everyone struggles with, so get it out of the way early on in development so it makes all future deployments much easier!

## Prerequisites

This tutorial **assumes** the following:

1. Your Django application code has **no errors**. Again, I recommend you follow these steps **at the beginning of your project**, so you already know this part is true!
2. You have an account on **GitHub and Heroku**.
3. You have installed the **Heroku CLI** and are authenticated.
4. You are able to use PostgreSQL locally. *This will mean different things for different readers:*
      - For Mac users: follow the instructions [here](https://postgresapp.com/) to download and install [Postgres.app](https://postgresapp.com/) and the associated command line tools.
      - Note that the **only** reason I am recommending Postgres.app here is because it is the tool I am the most familiar with in my experience, and it has worked great with no issues. However, if you would like more options (or are on a different operating system), please check out the tools listed on the [download section of the official PostgreSQL documentation](https://www.postgresql.org/download/).

## Part 1: Creating a New Django Project

1. Create a new public repository on GitHub. Add a ```.gitignore``` file for Python.

2. Clone the repo on GitHub onto your local machine.
    *(optional)*: at this step, you can add a few files to the ```.gitignore``` if you like:
    - if you're on macOS, you can add ```.DS_Store```
    - if you use Visual Studio Code, you can also add ```.vscode```
    Again you don't have to do this step, but *it just makes sense*, because these files don't actually do anything to meaningfully add/detract from your deployment.
3. Create a Python 3 virtual environment. Name it something that's on the ```.gitignore```, such as ```env```.
   For example the command you type into the CLI may look like this:
   ```python3 -m venv env```
   If you make a mistake here, that's okay. You can simply delete whatever folder was created on your filesystem (it will have the same name whatever you put after ```venv``` above), and try again.
   **NOTE**: if you want your virtual environment to be in Python 3, then enter this command using ```python3```. Afterwards once you are in your virtual environment, you will only need to use ```python``` (or you could just set an alias in your ```PATH``` but that's outside the scope of this tutorial.
4. Activate the said Python virutal environment. **Remain in the virtual environment for every following step!**
   Assuming you created your virtual environment like we did in Step 3, then you can simply enter: ```source env/bin activate```
5. Install the ```Django``` package. Explictly tell which Python interpreter you want to install the library with (it should be the one you're using for the virtual environment) by prefixing your ```pip install``` command with ```python -m```. The whole command may look something like this, for example:
      ```python -m pip install django```
6. (Optional) You can upgrade ```pip``` here if you like: ```python -m pip install --upgrade pip```
7. Start your Django project!

## Part 2: Tweaking Settings for Deployment

1. It's hide to set some environment variables!
   - We will be using the ```python-dotenv
   - add a ```.env``` file, on the same level as the ```manage.py``` file that was just created.
   - in the ```settings.py``` file under the inner project folder, add the following lines:

    ```python
    from dotenv import load_dotenv
    load_dotenv()
    ```

2. Add key-value pairs on your ```.env``` file for the following settings:
   - ```SECRET_KEY=```*whatever string is provided to you by the Django project installation*
   - ```DEBUG=True```
   - Being able to hide your environment variables will help you as a developer to protect sensitive information. This is not the only step in this tutorial in which we will be working with your ```.env```!
   *Rules of Thumb To Follow, when Setting Key-Value Pairs in the ```.env```*:
      - No spaces in the key
      - Don't use quotes anywhere!
      - Source: Make School's BEW 1.1 Lesson on ["Deployment Environments"](https://make-school-courses.github.io/BEW-1.1-RESTful-and-Resourceful-MVC-Architecture/Slides/11-Deployment-Environments/README#/4/2)
3. **REMEMBER**: Whenever we add an environment variable to the ```.env```, that's a greenlight for you to replace the hard-coded values in ```settings.py```. Do that now, for the two variables we just added in ```DEBUG``` and ```SECRET_KEY```. To be extra explict, you can also pass the call to ```os.getenv()``` into the constructor for whatever data type you want to be used in that variable, e.g. ```str()``` for settings other than ```DEBUG``` (which can be cast as a ```bool()``` instead).
For example, for the ```SECRET_KEY``` variable it will look something like this, in ```settings.py```:

    ```python
    SECRET_KEY = str(os.getenv('SECRET_KEY`)) # the string will be whatever key you put in .env
    ```

4. The next step is to create a new PostgreSQL database locally for this project, and that modify the ```DATABASES``` setting accordingly. Go into your ```psql``` terminal and create a new database for your project now, using the ```CREATE DATABASE <db_name>;``` command (and don't forget that semi-colon!More info: [W3Schools](https://www.w3schools.com/SQl/sql_create_db.asp) and the [PostgreSQL documentation](https://www.postgresql.org/docs/).

5. You can find more info on how to format this setting on the [Django documentation](https://docs.djangoproject.com/en/3.0/ref/settings/#databases), and it should roughly look something like this:

    ```python
    DATABASES = {
        'default': {
            'NAME': 'test',
            'ENGINE': 'django.db.backends.postgresql',
            'USER': 'postgres',
            'PASSWORD': str(os.getenv('DATABASE_PASSWORD')),
            'HOST': '',
            'PORT': 5432
        }
    }
    ```

    Notice in the code above, we have a new environment variable called ```DATABASE_PASSWORD```. Make sure to add a key-value pair for it in ```.env```!

6. Hold up! We have just set a PostgreSQL database in our *local* environment, not Heroku. We will use this later in order to check that the application code itself is working ok. But for right nowm let's set about **creating a Heroku app**, and provisioning a **remote database for production** (note: this step is technically *optional* for really small project):

## Part 3: Onward to Heroku

1. Create a new Heroku app, name it whatever you want. Verify it is created correctly by visiting the URL the Heroku CLI gives you.

    There shouldn't be anything too fancy on this page yet, apart from this welcome message from Heroku:
    ![Screenshot Heroku Welcome Message Goes Here](https://i.postimg.cc/50Y16Ms8/Screen-Shot-2020-05-27-at-11-05-41-AM.png)

2. Set a remote git branch to Heroku, using the following command, taken from the [Heroku Dev Center](https://devcenter.heroku.com/articles/git):

    ```python
    heroku git:remote -a <name of your Heroku app>
    ```

    Once you have completed this step, verify you have a remote branch to Heroku by entering the command ```git remote -v``` into your CLI.

3. Now it's take to on the database side of things. This can get really hairy, but for our purposes today we'll just use 3rd party libraries to show you the simplest use case of a PostgreSQL database for your Django app.

    Install the following libraries via the following commands with ```pip```:
    - ```psycopg2-binary```
    - ```dj-database-url```

4. Import ```dj_database_url``` at the beginning portion of your project settings file.

    Then, add the following to the bottom the same file:

    ```python
    db_from_env = dj_database_url.config()
    DATABASES['default'].update(db_from_env)

5. You should now be able to test your app running locally. Verify that there are no errors in your application code now, by running your database migrations and the server on your local machine:

    ```bash
    python manage.py migrate
    python manage.py runserver
    ```

6. Go back to your ```settings.py``` module now, and edit the ```ALLOWED_HOSTS``` setting to allow Heroku to host your app once it has been push:

    For example, if your Heroku app is named "uniqueprojectname", then what you'll put for this setting is the following:

    ```python
     ALLOWED_HOSTS = [
         'localhost',
         'uniqueprojectname.herokuapp.com',
    ]
    ```

7. Set up your ```STATIC_ROOT``` which will tell Heroku where to collect all your static assets (HTML, CSS, and JavaScript files) in production.

    Add this line somewhere in ```settings.py```, preferably below where the ```STATIC_URL``` is located:

    ```python
    STATIC_ROOT = os.path.join(BASE_DIR, 'static')
    ```

8. With all the above being done, you are nearly ready to deploy your app to Heroku. We just need to do a few more things to ensure consistency between your local and production environment. Fortunately, this process can also be expedited using yet *another 3rd party library.*

    - Install the ```django-heroku``` package now via ```pip```:

    ```python -m pip install django-heroku```
    - Following a process similar to what we did for the ```dj-database-urls``` packages, you should now import ```django-heroku``` at the top of the project settings module. Then at the bottom of the file, you need to call a specific function from the package like so. According to this page on the [Heroku documentation](https://devcenter.heroku.com/articles/django-app-configuration), this will "automatically configures your Django application to work on Heroku":

        ```python
        # other imports
        import django_heroku
        # rest of the file
        django_heroku.settings(locals())
        ```

9. Install the ```gunicorn``` package now as well, via ```pip```. The details of why you need this are outside the scope of this tutorial; but suffice to say Gunicorn (aka "Green Unicorn") will help the Python code to play nice with the configurations in the WSGI module, as well as HTTP.

    ```bash
    python -m pip install gunicorn
    ```

10. Procfile
11. remote settings

12. requirements

13. push

14. scale

## Part 4: Bonus Section

## Extra Resources

I hope that was helpful! Now you should check out whatever URL your project is now live on, to make sure it actually works. If it doesn't work - don't lose hope! Pushing to prod is a skill earned through perseverance (like all great skills). Please check out the following in order to help you debug whatever issues may still remain in your deployment:

1. The Heroku logs: enter ```heroku logs --tail``` into your CLI.
2. Check out the guides on [Django Deployment](https://make-school-courses.github.io/BEW-1.2-Authentication-and-Associations/#/Lessons/11-Deployment?id=60m--guided-tour-deploy-tutorial-on-heroku) and [Provisioning a Remote Database on Heroku](https://make-school-courses.github.io/BEW-1.2-Authentication-and-Associations/#/./Lessons/HowTo-DeployWithPostgres) from the BEW 1.2 class at Make School.
3. Read the [Heroku documentation](https://devcenter.heroku.com/articles/deploying-python) on deploying Django apps to the platform.

If you have comments, questions, or any other feedback for this gist please reach out to the author to suggest changes!
