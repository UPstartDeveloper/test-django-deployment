# How to Successfully Deploy a Django Project, on the FIRST PUSH!!

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
      ```
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
3. **REMEMBER**: Whenever we add an environment variable to the ```.env```, that's a greenlight for you to replace the hard-coded values in ```settings.py```. Do that now, for the two variables we just added in ```DEBUG``` and ```SECRET_KEY```.

For example, for the ```SECRET_KEY``` variable it will look something like this, in ```settings.py```:

```SECRET_KEY = os.getenv('SECRET_KEY`). # the string will be whatever key you put in .env```

## Extra Resources
I hope that was helpful! Now you should check out whatever URL your project is now live on, to make sure it actually works. If it doesn't work - don't lose hope! Pushing to prod is a skill earned through perseverance (like all great skills). Please check out the following in order to help you debug whatever issues may still remain in your deployment:

1. The Heroku logs: enter ```heroku logs --tail``` into your CLI.
2. Check out the guide on [Django Deployment](https://make-school-courses.github.io/BEW-1.2-Authentication-and-Associations/#/Lessons/11-Deployment?id=60m--guided-tour-deploy-tutorial-on-heroku) from the BEW 1.2 class at Make School.

If you have comments, questions, or any other feedback for this gist please reach out to the author to suggest changes!