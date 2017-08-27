Contributing to Laravel

Hey guys

A couple of days ago I started to build a small web app for fun and it was a drop box clone. It's called Yet Another Drop Box and as you guess, it's an online file hosting and sharing service. At the same time, I was also planning to try out Laravel 5.5 which is not officially released yet. So I thought I could try out the cutting edge of what Laravel got to offer.

After setting up the necessary things for the web app I changed the namespace of my app to YetAnother using the "app:name" artisan command which is one of Laravel awesome helper commands. The "app:name" command let you set the application namespace from the default App namespace to the one you specify.

Yesterday I was generating a model for representing 'file' and I saw "-a" option to the make:model command and the description said "-a, --all             Generate a migration, factory, and resource controller for the model" and I thought it was awesome because before 5.5 I use to pass "-cmr" to the "make:model" command to generate a resource controller and migration for a given model.  So I tried it out and generated the model with migration and a resource control but it also creates a file FileFactory file in database/factories. I open it up and it was a factory generator which we use to write in ModelFactory on 5.4. Then I saw a file called a UserFactory next to it and it was the factory generator for the User model which use to be shipped in ModelFactory before 5.4.

Then I realize it was a new feature on laravel 5.5. We use to store all our factory generators in one file which can be messy and get huge if you have a lot of models. Splitting it to multiple files was a cool thing and getting rid of ModelFactory was a smart move ... I appreciated Taylor as always and continue.

But I notice that the application namespace is not updated on UserFactory, It was still saying "App\User". So I executed the command Again as php artisan "app:name" YetAnother but it was not still changing. I know for sure this command uses to change the namespace in the ModelFactory on Laravel 5.4 but now it's not working on laravel 5.5-dev so I start poking around why it doesn't work.

First, I search for AppName and I found out the "app:name" command resides on 'Illuminate\Foundation\Console\AppNameCommand" class and I started skimming through the code. Since every command implements the handle method, I jump to the handle method and I saw "$this->setDatabaseFactoryNamespaces();" it looks convenient that the database factory is handled there so I jump to the setDatabaseFactoryNamespaces method.

On line 224 I found the method and I were shocked. it read as "$this->replaceIn(
            $this->laravel->databasePath().'/factories/ModelFactory.php',
            $this->currentRoot, $this->argument('name')
        );"

I thought why is it trying to change the namespace in ModelFactory. I asked Isn't ModelFactory gone for good? Then tried the command a couple of times with a debugger and finally, I realized it's a bug.

Since my install was a week old I said it might be fixed and I should update to get the latest commit. Therefore I update my composer dependencies and nothing changed. So I log in to GitHub and check it on the Laravel latest branch and I found out the bug was there too.

So I started digging when the regression happened and I found out it was 5 months ago when Taylor him self renamed the "ModelFactory.php" file to "UserFactory.php" with a commit message saying "rename ModelFactory to UserFactory to encourage separate files". But the "app:name" command was not updated accordingly of the changed. That poor little artisan command was still looking for ModelFactory.php file.

So I fixed the bug gladly. I searched all the files inside the database/factories and update the namespace on all of them iteratively. 

After that, I send a pull request to the laravel/framework repo which got merged after a couple of hours.

I want to give some pointer for those of you who want to contribute to big project

 1. First Read the contribution guideline before doing anything
 2. Try to find some useful improvements or bugs
 3. Don't be afraid to send a pull request, they won't bite
 4. Don't get offended if they didn't accept your pull request
 5. Most importantly be nice

Thanks for reading
PS. give me some feed back
