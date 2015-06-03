
[[DRAFT]]

# Overclocking Gitify workflow with Gulp

If you don't use Gitify for your MODX project yet – **you must**! Seriously! 
Okay. What is Gitify?

Gitify is bunch of tools (commands) that make life easier. With Gitify you can install MODX, install packages via one command, make backup, restore backup and of course extract whole site to files and build whole site from files. Sounds like magic. And it is, I cheked! :) 

Here official repository of project: https://github.com/modmore/Gitify

More information about usage you can find on [wiki](https://github.com/modmore/Gitify/wiki). I say that I use Gitify on all my latest project built on MODX. 

## Useful, but not fast

Gitify awesome tool, but written on PHP, that not allow use `watch` command, as it do nodejs (or I just was banned by Google). So, usually I make next steps in my workflow:

1. fix some files
2. run `gitify build`
3. check result
4. go to 1

Not difficult, but take time. You should open IDE, fix files, then open terminal, run command, open browser and check result. Extra useless actions.
Whould be good if changes can applied by self, after save. I solve this problem and below will say how.

## Preparing environment

### Install `nodejs` and `npm`

At first you should install nodejs and npm. We will use watch command and tasks for run gitify commands.
How it do you can find on [official page of nodejs](https://nodejs.org/)

### Create `package.json` on root folder of project

Usualy I setup node_modules to `assets/theme` folder for build with gulp all styles and scripts, but now we use gulp only for build MODX with Gitify.

```json
{
    "name": "sitename.com",
    "version": "1.0.0",
    "devDependencies": {
        "gulp": "*",
        "gulp-shell": "*",
        "gulp-tap": "*",
        "gulp-watch": "*"
    }
}
```

gulp - [Gulp](http://gulpjs.com/)
gulp-shell - for run command from shell
gulp-tap - plugin for tap files to pipeline
gulp-watch - watch command for check MODX files updates

### Install gulp and dependencies

Now you need install Gulp in system and install local dependecies.
`npm install -g gulp`

And now just run `npm install` for installing all dependencies.

### Gulpfile - heart of our mission

////

## пишем gulpfile
подробные пояснения что и зачем

I collected all files for usage in Gist (https://gist.github.com/Alroniks/1466b67a648949a1bbea). Feel free to give me feedback.
Thank you.