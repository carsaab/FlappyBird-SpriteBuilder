<!DOCTYPE html>
<html lang="en">

<h1 id="eecs485-getting-started">Running BatChords</h1>
credit: These instructions are from Andrew DeOrio's EECS 485 Project 3 Setup tutorial. We are using the same environment.

<p>This document will guide you through setting up your computer for local development on OSX or Linux, or through setting up a virtual machine if using Windows or another operating system.</p>

<p>For local development, clone or download the repo <code class="highlighter-rouge">git clone https://github.com/jmzuid/BatChords</code>.  Throughout this document, we’ll refer to this root project directory as <code class="highlighter-rouge">$PROJECT_ROOT</code>.</p>

<p>This tutorial will walk you through</p>
<ul>
  <li><a href="#restarting-this-tutorial">Restarting this tutorial</a></li>
  <li><a href="#install-toolchain">Install toolchain</a></li>
  <li><a href="#create-a-python-virtual-environment">Create a Python virtual environment</a></li>
  <br>
  <li><a href="#install-back-end">Install back end</a></li>
  <li><a href="#rest-api">REST API</a></li>
  <li><a href="#install-front-end">Install front end</a></li>
  <li><a href="#understanding-the-front-end-code">Understanding the front end code</a></li>
  <li><a href="#developer-tools">Developer Tools</a></li>
</ul>

<h2 id="restarting-this-tutorial">Restarting this tutorial</h2>
<p>If you made a mistake with these Python instructions, here’s how to start over.  First, close your shell and reopen it to ensure that environment variables are reset.  Then, delete the virtual environment.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">cd</span> <span class="nv">$PROJECT_ROOT</span>
<span class="gp">$</span> rm <span class="nt">-rf</span> env
</code></pre></div></div>

<p>After you’ve deleted your virtual environment, you can create it again.  Resume the instructions with <a href="#create-a-python-virtual-environment">Create a Python virtual environment</a>.</p>

<h2 id="install-toolchain">Install toolchain</h2>
<p>Install the tools based on your operating system.</p>

<h3 id="osx">OSX</h3>
<p>If you run OSX, install the <a href="https://brew.sh/">Homebrew package manager</a>.  Then, use Homebrew to install a few packages like Python3.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> /usr/bin/ruby <span class="nt">-e</span> <span class="s2">"</span><span class="k">$(</span>curl <span class="nt">-fsSL</span> https://raw.githubusercontent.com/Homebrew/install/master/install<span class="k">)</span><span class="s2">"</span>
<span class="gp">$</span> brew install python3 git tree wget
</code></pre></div></div>

<h3 id="linux">Linux</h3>
<p>These instructions work for Debian-based systems like Ubuntu.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">sudo </span>apt-get update
<span class="gp">$</span> <span class="nb">sudo </span>apt-get install python3-venv python3-wheel python3-setuptools git tree
<span class="gp">$</span> <span class="nb">sudo </span>apt-get install default-jre
</code></pre></div></div>

<h3 id="linux-virtual-machine">Linux Virtual Machine</h3>
<p>If you are developing locally, you can skip this subsection and move on to <a href="#create-a-python-virtual-environment">Create a Python virtual environment</a>.</p>

<p>First, install <a href="https://git-scm.com/download/win">Git Bash</a>, <a href="https://www.vagrantup.com/downloads.html">Vagrant</a> and <a href="https://www.virtualbox.org/wiki/Downloads">VirtualBox</a>.  When we use the command line later in this section, we’re assuming that you are using Git Bash, not the Windows command prompt.</p>

<p>Vagrant lets us describe an entire virtual computer using a configuration file.  Usually, the configuration lives inside your project’s root directory.  Create a sample configuration file for an Ubuntu 16.04 virtual machine:</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">cd</span> <span class="nv">$PROJECT_ROOT</span>/
<span class="gp">$</span> vagrant init bento/ubuntu-16.04
<span class="go">A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
</span><span class="gp">$</span> <span class="nb">ls </span>Vagrantfile
<span class="go">Vagrantfile
</span></code></pre></div></div>

<p>Now, edit the <code class="highlighter-rouge">Vagrantfile</code> to forward a port for your development webserver.  This lets you access a webserver running inside your guest virtual machine using a web browser running on your host operating system.  Find this line, uncomment it, and change the port numbers.</p>
<div class="language-ruby highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="n">config</span><span class="p">.</span><span class="nf">vm</span><span class="p">.</span><span class="nf">network</span> <span class="s2">"forwarded_port"</span><span class="p">,</span> <span class="ss">guest: </span><span class="mi">8000</span><span class="p">,</span> <span class="ss">host: </span><span class="mi">8000</span>
</code></pre></div></div>

<p>Before starting your virtual machine, make sure you don’t have any stale virtual machines <em>for this project</em> laying around.  You can also use the VirtualBox graphical user interface (GUI) for this.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> vboxmanage list vms
</code></pre></div></div>

<p>Start your virtual machine.  This is an entire second operating system running like it’s a program.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> vagrant up
<span class="go">Bringing machine 'default' up with 'virtualbox' provider...
</span></code></pre></div></div>

<p>Check if VM is running:</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> vagrant status
<span class="go">Current machine states:

default                   running (virtualbox)
</span></code></pre></div></div>

<p>Log in to your virtual machine using SSH</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> vagrant ssh
<span class="go">Welcome to Ubuntu 16.04.2 LTS (GNU/Linux 4.4.0-75-generic x86_64)

</span><span class="gp">vagrant@vagrant:~$</span>
</code></pre></div></div>

<p>All commands for the remainder of this tutorial are executed inside your virtual machine.  By default, there is a shared directory between the guest and host operating systems.  Inside the VM, it’s located at <code class="highlighter-rouge">/vagrant</code>.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">cd</span> /vagrant/
<span class="gp">$</span> <span class="nb">ls</span>
<span class="go">Vagrantfile
</span></code></pre></div></div>

<p>You can install programs with <code class="highlighter-rouge">apt-get</code>.  Let’s install a few tools that this tutorial will use later.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> apt-get update
<span class="gp">$</span> apt-get install python3-venv python3-wheel python3-setuptools git tree
<span class="gp">$</span> <span class="nb">sudo </span>apt-get install default-jre
</code></pre></div></div>

<p>Let’s fire up a development web server inside our VM.  This is a <em>static file server</em>.  It listens for requests, and responds to a request with a copy of a file from the server’s file system.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">cd</span> /vagrant
<span class="gp">$</span> python3 <span class="nt">-m</span> http.server 8000
<span class="go">Serving HTTP on 0.0.0.0 port 8000 ...
</span></code></pre></div></div>

<p>After starting the server, navigate to <a href="http://localhost:8000">http://localhost:8000</a> using a web browser on your host OS.  You should see something like this:</p>

<p><img src="https://eecs485staff.github.io/p1-insta485-static/images/dev-server.png" alt="dev server screenshot" /></p>

<p>After you’re done with a coding session, log out of your SSH session, and shut down the VM.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">vagrant@vagrant $</span> <span class="nb">exit</span>
<span class="gp">$</span> vagrant halt
<span class="gp">==&gt;</span> default: Attempting graceful shutdown of VM...
</code></pre></div></div>

<p><strong>WARNING</strong> This project uses <code class="highlighter-rouge">npm</code> to install JavaScript libraries, shared directories won’t work.  Use a  <code class="highlighter-rouge">$PROJECT_ROOT</code> that is inside the VM and <em>not</em> shared.  A good choice is <code class="highlighter-rouge">/home/vagrant</code>. </p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> vagrant ssh
<span class="gp">$</span> <span class="nb">cd</span> /home/vagrant/
<span class="gp">$</span> git clone https://github.com/jmzuid/BatChords
<span class="gp">$</span> <span class="nb">cd </span>BatChords
<span class="gp">$</span> <span class="nb">pwd</span>
<span class="gp">/home/vagrant/BatChords  #</span> this is your <span class="nv">$PROJECT_ROOT</span>
</code></pre></div></div>

<h2 id="create-a-python-virtual-environment">Create a Python virtual environment</h2>
<p>This section will help you install the Python tools and libraries locally, which won’t affect Python tools and libraries installed elsewhere on your computer.</p>

<p>After finishing this section, you’ll have a folder called <code class="highlighter-rouge">env/</code> that contains all the Python libraries you need for this project.  Those libraries won’t interfere with other Python projects on your computer.</p>

<p>We’re starting in the root of our project directory.  If you’re using a VM, don’t forget to <code class="highlighter-rouge">vagrant ssh</code>.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">cd</span> <span class="nv">$PROJECT_ROOT</span>
</code></pre></div></div>

<p>Set up a virtual environment.  (More on <a href="https://docs.python.org/3/library/venv.html">venv and the creation of virtual environments</a>)</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> python3 <span class="nt">-m</span> venv env
</code></pre></div></div>

<p><strong>NOTE</strong> If you’re on OSX and have previously installed Anaconda, be sure to use the full path to the Python executable.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> which python
<span class="go">/Users/awdeorio/anaconda/bin/python
</span><span class="gp">$</span> rm <span class="nt">-rf</span> env  <span class="c"># Remove environment from previous step and start over</span>
<span class="gp">$</span> /usr/local/bin/python3 <span class="nt">-m</span> venv env
</code></pre></div></div>

<p><strong>NOTE</strong> If the <code class="highlighter-rouge">PYTHONPATH</code> environment variable is set, then this can cause problems.  Check this when you first start a new shell.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">echo</span> <span class="nv">$PYTHONPATH</span>  <span class="c"># Output isn't blank, problem!</span>
<span class="go">/Users/awdeorio/anaconda/lib/
</span><span class="gp">$</span> rm <span class="nt">-rf</span> env  <span class="c"># Remove environment from previous step and start over</span>
<span class="gp">$</span> <span class="nb">unset </span>PYTHONPATH
<span class="gp">$</span> python3 <span class="nt">-m</span> venv env
</code></pre></div></div>

<p>Activate virtual environment.  You’ll need to do this every time you start a new shell.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">source </span>env/bin/activate
</code></pre></div></div>

<p>We now have a complete local environment for Python.  Everything lives in one directory.  Environment variables point to this virtual environment.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">echo</span> <span class="nv">$VIRTUAL_ENV</span>
<span class="go">/Users/carolinesaab/Documents/eecs/eecs498/BatChords/env
</span></code></pre></div></div>

<p>We have a Python interpreter installed inside the virtual environment.  <code class="highlighter-rouge">which python</code> tells you exactly which python executable file will be used when you type <code class="highlighter-rouge">python</code>.  Because we’re in a virtual environment, there’s more than one option!</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> which python
<span class="go">/Users/carolinesaab/Documents/eecs/eecs498/BatChords/env/bin/python
</span><span class="gp">$</span> which <span class="nt">-a</span> python  <span class="c"># Show all available python executables</span>
<span class="go">/Users/carolinesaab/Documents/eecs/eecs498/BatChords/env/bin/python
/usr/bin/python
</span></code></pre></div></div>

<p>There’s a package manager for Python installed in the virtual environment.  That will help us install Python libraries later.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> which pip
<span class="go">/Users/carolinesaab/Documents/eecs/eecs498/BatChords/env/bin/pip
</span><span class="gp">$</span> pip <span class="nt">--version</span>
<span class="gp">pip 9.0.1 from /Users/carolinesaab/Documents/eecs/eecs498/BatChords/env/lib/python3.6/site-packages (python 3.6)  #</span> Your version may be different
</code></pre></div></div>

<p>Python packages live in the virtual environment.  We can see that Python’s own tools are already installed (<code class="highlighter-rouge">pip</code> and <code class="highlighter-rouge">setuptools</code>).</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">ls </span>env/lib/python3.6/site-packages/
<span class="go">pip
setuptools
</span><span class="c">...
</span></code></pre></div></div>

<p>Upgrade the Python tools in your virtual environment</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> pip install <span class="nt">--upgrade</span> pip setuptools wheel
</code></pre></div></div>

<p>For developers: install HTML5 Validator to check style</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> pip install html5validator
<span class="gp">$</span> html5validator <span class="nt">--version</span>
<span class="gp">html5validator 0.2.6         #</span> Your version may be different
</code></pre></div></div>


<h2 id="install-back-end">Install back end</h2>

<p>Next, we’ll install the back end app in developer mode. Make sure that you still see <code>(env)</code> in the terminal, meaning your virtual environment is activated. Otherwise run <code>env/bin/activate</code> to reactivate it.</p>

<span class="gp">$</span> pip install <span class="nt">-e</span> <span class="nb">.</span>
</code></pre></div></div>

<h2 id="install-front-end">Install front end</h2>
<p>In the previous sections, we coded our server back end using Python and the Flask library.  The front end is comprised by JavaScript code running on the client’s browser.  The front end code modifies what the user sees in the browser.</p>

<p>We need to install a tool chain for our front end.  The tool chain provides a few benefits:</p>
<ul>
  <li>Download and install JavaScript libraries</li>
  <li>Package JavaScript libraries together with our own code for deployment by our own back end server.</li>
  <li>Compile modern <a href="http://es6-features.org/">ES6</a> javascript to portable ES5.
If you’re interested, here’s <a href="https://benmccormick.org/2015/09/14/es5-es6-es2016-es-next-whats-going-on-with-javascript-versioning/">a little JavaScript history</a>.</li>
  <li>Compile <a href="https://facebook.github.io/react/docs/introducing-jsx.html">JSX syntactic sugar</a> to portable ES5.  JSX is a language extension that lets you embed HTML in a JavaScript program.  It’s like jinja2 templates, but in JavaScript.</li>
  <li>Minify source code (optional)</li>
  <li>Obfuscate source code (optional)</li>
  <li>Concatenate everything into one JS file for final client consumption</li>
</ul>

<p>Our tool chain will consist of a JavaScript interpreter (<code class="highlighter-rouge">node</code>) and a package manager (<code class="highlighter-rouge">npm</code>).  In the language of the SAT, <code class="highlighter-rouge">node</code> is to <code class="highlighter-rouge">python</code> as <code class="highlighter-rouge">npm</code> is to <code class="highlighter-rouge">pip</code>.  Keep in mind that we aren’t running any servers using JavaScript.  It’s just that the tool chain used by JavaScript programmers happens to be written in JavaScript.  Thus, we need a JavaScript interpreter to run the tool chain.</p>

<h3 id="install-javascript-virtual-environment">Install JavaScript virtual environment</h3>
<p>We’re working in the project 3 root directory, with the Python virtual environment activated.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">cd </span>p3-insta485-clientside/
<span class="gp">$</span> <span class="nb">source </span>env/bin/activate
</code></pre></div></div>

<p>We want to install our front end tool chain into our existing virtual environment.  <code class="highlighter-rouge">nodeenv</code> is a Python tool for merging a Python virtual environment with a JavaScript virtual environment.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> pip install nodeenv
<span class="gp">$</span> nodeenv <span class="nt">--python-virtualenv</span>
<span class="gp">$</span> <span class="nb">source </span>env/bin/activate  <span class="c"># Yes, do this again after nodeenv</span>
</code></pre></div></div>

<h3 id="understanding-the-virtual-environment">Understanding the virtual environment</h3>
<p>We now have a complete local environment for both our back end tool chain and our front end tool chain.  Environment variables point to these virtual environments.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">echo</span> <span class="nv">$VIRTUAL_ENV</span>
<span class="go">/Users/awdeorio/src/eecs485/p3-insta485-clientside/env
</span><span class="gp">$</span> <span class="nb">echo</span> <span class="nv">$NODE_VIRTUAL_ENV</span>
<span class="go">/Users/awdeorio/src/eecs485/p3-insta485-clientside/env
</span></code></pre></div></div>

<p>We have two interpreters, one for Python and one for JavaScript.  Both are installed in the virtual environment.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> which python
<span class="go">/Users/awdeorio/src/eecs485/p3-insta485-clientside/env/bin/python
</span><span class="gp">$</span> which node
<span class="go">/Users/awdeorio/src/eecs485/p3-insta485-clientside/env/bin/node
</span></code></pre></div></div>

<p>There are two package managers, one for Python and one for JavaScript.  Both are installed in the virtual environment.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> which pip
<span class="go">/Users/awdeorio/src/eecs485/p3-insta485-clientside/env/bin/pip
</span><span class="gp">$</span> which npm
<span class="go">/Users/awdeorio/src/eecs485/p3-insta485-clientside/env/bin/npm
</span></code></pre></div></div>

<p>Python packages live in the virtual environment.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">ls </span>env/lib/python3.6/site-packages/
<span class="go">Flask-0.12.2.dist-info
Jinja2-2.9.6.dist-info
</span><span class="c">...
</span></code></pre></div></div>

<p>Globally installed JavaScript packages live in the virtual environment.  So far, the <code class="highlighter-rouge">npm</code> package manager is the only globally installed JavaScript package.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">echo</span> <span class="nv">$NODE_PATH</span>
<span class="go">/Users/awdeorio/src/eecs485/p3-insta485-clientside/env/lib/node_modules
</span><span class="gp">$</span> <span class="nb">ls </span>env/lib/node_modules/
<span class="go">npm
</span></code></pre></div></div>

<h3 id="javascript-packages">JavaScript packages</h3>
<p>Next, we’ll create a JavaScript package for our front end source code.</p>

<p>Source code will live in <code class="highlighter-rouge">insta485/js/</code>.  We’re going to be using the JSX extension to JavaScript to embed HTML inside JavaScript code, so name the files with <code class="highlighter-rouge">.jsx</code> extensions.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> mkdir insta485/js/
<span class="gp">$</span> touch insta485/js/main.jsx
<span class="gp">$</span> touch insta485/js/likes.jsx
</code></pre></div></div>

<p>Like Python’s <code class="highlighter-rouge">setup.py</code>, JavaScript packages have a configuration file.  It’s called <code class="highlighter-rouge">package.json</code>.  Here’s an example.  <em>NOTE</em>: be sure to use the <code class="highlighter-rouge">package.json</code> provided in the starter files, which contains more up-to-date package versions.</p>
<div class="language-javascript highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="p">{</span>
  <span class="s2">"name"</span><span class="p">:</span> <span class="s2">"insta485"</span><span class="p">,</span>
  <span class="s2">"version"</span><span class="p">:</span> <span class="s2">"0.1.0"</span><span class="p">,</span>
  <span class="s2">"description"</span><span class="p">:</span> <span class="s2">"insta485 front end"</span><span class="p">,</span>
  <span class="s2">"main"</span><span class="p">:</span> <span class="s2">"insta485/js/main.jsx"</span><span class="p">,</span>
  <span class="s2">"author"</span><span class="p">:</span> <span class="s2">"FIXME"</span><span class="p">,</span>
  <span class="s2">"license"</span><span class="p">:</span> <span class="s2">""</span><span class="p">,</span>
  <span class="s2">"devDependencies"</span><span class="p">:</span> <span class="p">{</span>
    <span class="s2">"babel-core"</span><span class="p">:</span> <span class="s2">"^6.4.5"</span><span class="p">,</span>
    <span class="s2">"babel-eslint"</span><span class="p">:</span> <span class="s2">"^7.2.3"</span><span class="p">,</span>
    <span class="s2">"babel-loader"</span><span class="p">:</span> <span class="s2">"^7.0.0"</span><span class="p">,</span>
    <span class="s2">"babel-preset-es2015"</span><span class="p">:</span> <span class="s2">"^6.3.13"</span><span class="p">,</span>
    <span class="s2">"babel-preset-react"</span><span class="p">:</span> <span class="s2">"^6.3.13"</span><span class="p">,</span>
    <span class="s2">"eslint"</span><span class="p">:</span> <span class="s2">"^3.19.0"</span><span class="p">,</span>
    <span class="s2">"webpack"</span><span class="p">:</span> <span class="s2">"^2.6.1"</span>
  <span class="p">},</span>
  <span class="s2">"dependencies"</span><span class="p">:</span> <span class="p">{</span>
    <span class="s2">"prop-types"</span><span class="p">:</span> <span class="s2">"^15.5.10"</span><span class="p">,</span>
    <span class="s2">"moment"</span><span class="p">:</span> <span class="s2">"^2.10.0"</span><span class="p">,</span>
    <span class="s2">"react"</span><span class="p">:</span> <span class="s2">"^15.5.4"</span><span class="p">,</span>
    <span class="s2">"react-dom"</span><span class="p">:</span> <span class="s2">"^15.5.4"</span>
  <span class="p">}</span>
<span class="p">}</span>
</code></pre></div></div>

<p>To ensure you have the exact same versions of JavaScript libraries as we do, copy <code class="highlighter-rouge">package-lock.json</code> and <code class="highlighter-rouge">package.json</code> from the <a href="/p3-insta485-clientside/starter_files.tar.gz">starter files</a> to <code class="highlighter-rouge">p3-insta485-clientside/package-lock.json</code> and <code class="highlighter-rouge">p3-insta485-clientside/package.json</code> respectively:</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> cp starter_files/package-lock.json package-lock.json
<span class="gp">$</span> cp starter_files/package.json package.json
</code></pre></div></div>

<p>Our front end JavaScript package will use the <code class="highlighter-rouge">react</code> library, which in turn relies on several dependencies of its own.  We’ll use <code class="highlighter-rouge">npm</code> to install these dependencies. At this point, you should be within the <code class="highlighter-rouge">p3-insta485-clientside</code> directory.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> npm install <span class="nb">.</span>
</code></pre></div></div>

<p>The previous command installed dependencies and development dependencies.  There are two command line utilities that we’ll use in development.  A JavaScript compiler (transpiler) called <code class="highlighter-rouge">webpack</code> and a linter called <code class="highlighter-rouge">eslint</code>.  Unfortunately, <code class="highlighter-rouge">npm</code> does not provide the nice convenience of adding the tools to our path.  We’ll have to specify the path manually.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> ./node_modules/.bin/eslint <span class="nt">--version</span>
<span class="go">v4.8.0
</span><span class="gp">$</span> ./node_modules/.bin/webpack <span class="nt">--version</span>
<span class="go">3.6.0
</span></code></pre></div></div>

<p>The source code for module dependencies is stored in <code class="highlighter-rouge">./node_modules/</code> and their exact versions are listed in <code class="highlighter-rouge">package-lock.json</code>.  This is what the root project 3 directory should look like now.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> <span class="nb">ls</span>
<span class="go">bin  insta485           node_modules  package-lock.json  sql            starter_files.tar.gz
env  insta485.egg-info  package.json  setup.py           starter_files  var
</span></code></pre></div></div>

<p>What just happened?  Our module’s dependencies were described by <code class="highlighter-rouge">package.json</code>, which is an input to <code class="highlighter-rouge">npm</code>.  <code class="highlighter-rouge">package.json</code> is to JavaScript packages as <code class="highlighter-rouge">setup.py</code> is to Python packages.  The dependencies for our custom package were installed right next door to <code class="highlighter-rouge">./package.json</code> in <code class="highlighter-rouge">./node_modules/</code>.  Note that this is distinct from <code class="highlighter-rouge">env/lib/node_modules/</code>.  The files in <code class="highlighter-rouge">./node_modules/</code> will later be bundled together for distribution by our web server, while <code class="highlighter-rouge">env/lib/node_modules/</code> will not be distributed.</p>

<h3 id="writing-and-transpiling-javascript-code">Writing and transpiling JavaScript code</h3>
<p>In this section, we will code a very small JavaScript module that fetches the number of likes for a post from our REST API and displays it.  We’ll write modern ES6 source code and transpile it to  older ES5.  In a later section, we’ll go back over this source code and explain in more detail about how it works.</p>

<p>Write <code class="highlighter-rouge">insta485/js/likes.jsx</code>:</p>
<div class="language-javascript highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">import</span> <span class="nx">React</span> <span class="k">from</span> <span class="s1">'react'</span><span class="p">;</span>
<span class="k">import</span> <span class="nx">PropTypes</span> <span class="k">from</span> <span class="s1">'prop-types'</span><span class="p">;</span>

<span class="kd">class</span> <span class="nx">Likes</span> <span class="kd">extends</span> <span class="nx">React</span><span class="p">.</span><span class="nx">Component</span> <span class="p">{</span>
  <span class="cm">/* Display number of likes a like/unlike button for one post
   * Reference on forms https://facebook.github.io/react/docs/forms.html
   */</span>

  <span class="kd">constructor</span><span class="p">(</span><span class="nx">props</span><span class="p">)</span> <span class="p">{</span>
    <span class="c1">// Initialize mutable state</span>
    <span class="k">super</span><span class="p">(</span><span class="nx">props</span><span class="p">);</span>
    <span class="k">this</span><span class="p">.</span><span class="nx">state</span> <span class="o">=</span> <span class="p">{</span> <span class="na">num_likes</span><span class="p">:</span> <span class="mi">0</span><span class="p">,</span> <span class="na">logname_likes_this</span><span class="p">:</span> <span class="kc">false</span> <span class="p">};</span>
  <span class="p">}</span>

  <span class="nx">componentDidMount</span><span class="p">()</span> <span class="p">{</span>
    <span class="c1">// Call REST API to get number of likes</span>
    <span class="nx">fetch</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">props</span><span class="p">.</span><span class="nx">url</span><span class="p">,</span> <span class="p">{</span> <span class="na">credentials</span><span class="p">:</span> <span class="s1">'same-origin'</span> <span class="p">})</span>
    <span class="p">.</span><span class="nx">then</span><span class="p">((</span><span class="nx">response</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
      <span class="k">if</span> <span class="p">(</span><span class="o">!</span><span class="nx">response</span><span class="p">.</span><span class="nx">ok</span><span class="p">)</span> <span class="k">throw</span> <span class="nb">Error</span><span class="p">(</span><span class="nx">response</span><span class="p">.</span><span class="nx">statusText</span><span class="p">);</span>
      <span class="k">return</span> <span class="nx">response</span><span class="p">.</span><span class="nx">json</span><span class="p">();</span>
    <span class="p">})</span>
    <span class="p">.</span><span class="nx">then</span><span class="p">((</span><span class="nx">data</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
      <span class="k">this</span><span class="p">.</span><span class="nx">setState</span><span class="p">({</span>
        <span class="na">num_likes</span><span class="p">:</span> <span class="nx">data</span><span class="p">.</span><span class="nx">likes_count</span><span class="p">,</span>
        <span class="na">logname_likes_this</span><span class="p">:</span> <span class="nx">data</span><span class="p">.</span><span class="nx">logname_likes_this</span><span class="p">,</span>
      <span class="p">});</span>
    <span class="p">})</span>
    <span class="p">.</span><span class="k">catch</span><span class="p">(</span><span class="nx">error</span> <span class="o">=&gt;</span> <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="nx">error</span><span class="p">));</span>  <span class="c1">// eslint-disable-line no-console</span>
  <span class="p">}</span>

  <span class="nx">render</span><span class="p">()</span> <span class="p">{</span>
    <span class="c1">// Render number of likes</span>
    <span class="k">return</span> <span class="p">(</span>
      <span class="o">&lt;</span><span class="nx">div</span> <span class="nx">className</span><span class="o">=</span><span class="s2">"likes"</span><span class="o">&gt;</span>
        <span class="o">&lt;</span><span class="nx">p</span><span class="o">&gt;</span><span class="p">{</span><span class="k">this</span><span class="p">.</span><span class="nx">state</span><span class="p">.</span><span class="nx">num_likes</span><span class="p">}</span> <span class="nx">like</span><span class="p">{</span><span class="k">this</span><span class="p">.</span><span class="nx">state</span><span class="p">.</span><span class="nx">num_likes</span> <span class="o">!==</span> <span class="mi">1</span> <span class="p">?</span> <span class="s1">'s'</span> <span class="p">:</span> <span class="s1">''</span><span class="p">}</span><span class="o">&lt;</span><span class="sr">/p</span><span class="err">&gt;
</span>      <span class="o">&lt;</span><span class="sr">/div</span><span class="err">&gt;
</span>    <span class="p">);</span>
  <span class="p">}</span>
<span class="p">}</span>

<span class="nx">Likes</span><span class="p">.</span><span class="nx">propTypes</span> <span class="o">=</span> <span class="p">{</span>
  <span class="na">url</span><span class="p">:</span> <span class="nx">PropTypes</span><span class="p">.</span><span class="nx">string</span><span class="p">.</span><span class="nx">isRequired</span><span class="p">,</span>
<span class="p">};</span>

<span class="k">export</span> <span class="k">default</span> <span class="nx">Likes</span><span class="p">;</span>
</code></pre></div></div>

<p>Write <code class="highlighter-rouge">insta485/js/main.jsx</code>:</p>
<div class="language-javascript highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">import</span> <span class="nx">React</span> <span class="k">from</span> <span class="s1">'react'</span><span class="p">;</span>
<span class="k">import</span> <span class="nx">ReactDOM</span> <span class="k">from</span> <span class="s1">'react-dom'</span><span class="p">;</span>
<span class="k">import</span> <span class="nx">Likes</span> <span class="k">from</span> <span class="s1">'./likes'</span><span class="p">;</span>

<span class="nx">ReactDOM</span><span class="p">.</span><span class="nx">render</span><span class="p">(</span>
  <span class="o">&lt;</span><span class="nx">Likes</span> <span class="nx">url</span><span class="o">=</span><span class="s2">"/api/v1/p/1/likes/"</span> <span class="o">/&gt;</span><span class="p">,</span>
  <span class="nb">document</span><span class="p">.</span><span class="nx">getElementById</span><span class="p">(</span><span class="s1">'reactEntry'</span><span class="p">),</span>
<span class="p">);</span>
</code></pre></div></div>

<p>Now we need to put <code class="highlighter-rouge">reactEntry</code> somewhere in our HTML code and load the JavaScript.  Add this to the <code class="highlighter-rouge">&lt;body&gt;</code> of your template for the main page <code class="highlighter-rouge">insta485/templates/index.html</code>.  Also, go ahead and delete all the jinja template code that displays the feed.</p>
<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code>
...
<span class="nt">&lt;body&gt;</span>
  <span class="c">&lt;!-- Plain old HTML and jinja2 nav bar goes here --&gt;</span>


  <span class="nt">&lt;div</span> <span class="na">id=</span><span class="s">"reactEntry"</span><span class="nt">&gt;</span>
    Loading ...
  <span class="nt">&lt;/div&gt;</span>
  <span class="c">&lt;!-- Load JavaScript --&gt;</span>
  <span class="nt">&lt;script </span><span class="na">type=</span><span class="s">"text/javascript"</span> <span class="na">src=</span><span class="s">"{{ url_for('static', filename='js/bundle.js') }}"</span><span class="nt">&gt;&lt;/script&gt;</span>
<span class="nt">&lt;/body&gt;</span>
...

</code></pre></div></div>

<p>Notice that the HTML code asks for <code class="highlighter-rouge">bundle.js</code>, which is the output of our front end build process.  The inputs to the front end build process are the JavaScript files in <code class="highlighter-rouge">insta485/js/</code>.  The output is a single JavaScript file that is completely self-contained with no dependencies, <code class="highlighter-rouge">insta485/static/js/bundle.js</code>.</p>

<p>The build process is described by <code class="highlighter-rouge">webpack.config.js</code>, which should live in the project root directory. An example of <code class="highlighter-rouge">webpack.config.js</code> is provided here:</p>
<div class="language-javascript highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">const</span> <span class="nx">path</span> <span class="o">=</span> <span class="nx">require</span><span class="p">(</span><span class="s1">'path'</span><span class="p">);</span>

<span class="nx">module</span><span class="p">.</span><span class="nx">exports</span> <span class="o">=</span> <span class="p">{</span>
  <span class="na">entry</span><span class="p">:</span> <span class="s1">'./insta485/js/main.jsx'</span><span class="p">,</span>
  <span class="na">output</span><span class="p">:</span> <span class="p">{</span>
    <span class="na">path</span><span class="p">:</span> <span class="nx">path</span><span class="p">.</span><span class="nx">join</span><span class="p">(</span><span class="nx">__dirname</span><span class="p">,</span> <span class="s1">'/insta485/static/js/'</span><span class="p">),</span>
    <span class="na">filename</span><span class="p">:</span> <span class="s1">'bundle.js'</span><span class="p">,</span>
  <span class="p">},</span>
  <span class="na">module</span><span class="p">:</span> <span class="p">{</span>
    <span class="na">loaders</span><span class="p">:</span> <span class="p">[</span>
      <span class="p">{</span>
        <span class="c1">// Test for js or jsx files</span>
        <span class="na">test</span><span class="p">:</span> <span class="sr">/</span><span class="se">\.</span><span class="sr">jsx</span><span class="se">?</span><span class="sr">$/</span><span class="p">,</span>
        <span class="na">loader</span><span class="p">:</span> <span class="s1">'babel-loader'</span><span class="p">,</span>
        <span class="na">query</span><span class="p">:</span> <span class="p">{</span>
          <span class="c1">// Convert ES6 syntax to ES5 for browser compatibility</span>
          <span class="na">presets</span><span class="p">:</span> <span class="p">[</span><span class="s1">'es2015'</span><span class="p">,</span> <span class="s1">'react'</span><span class="p">],</span>
        <span class="p">},</span>
      <span class="p">},</span>
    <span class="p">],</span>
  <span class="p">},</span>
  <span class="na">resolve</span><span class="p">:</span> <span class="p">{</span>
    <span class="na">extensions</span><span class="p">:</span> <span class="p">[</span><span class="s1">'.js'</span><span class="p">,</span> <span class="s1">'.jsx'</span><span class="p">],</span>
  <span class="p">},</span>
<span class="p">};</span>
</code></pre></div></div>

<p>We’ll use the latest <code class="highlighter-rouge">webpack.config.js</code> from the starter files:</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> cp starter_files/webpack.config.js webpack.config.js
</code></pre></div></div>

<p>Run the front end build process.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> ./node_modules/.bin/webpack
<span class="go">Hash: 94d02a55e475959ef08d
Version: webpack 2.6.1
Time: 4910ms
    Asset    Size  Chunks                    Chunk Names
bundle.js  769 kB       0  [emitted]  [big]  main
   [0] ./~/process/browser.js 5.45 kB {0} [built]
  [15] ./~/react/lib/ReactElement.js 11.6 kB {0} [built]
  [17] ./~/react-dom/lib/ReactReconciler.js 6.29 kB {0} [built]
  [18] ./~/react/lib/React.js 5.11 kB {0} [built]
  [50] ./~/react/react.js 55 bytes {0} [built]
  [83] ./insta485/js/likes.jsx 3.52 kB {0} [built]
  [84] ./~/react-dom/index.js 58 bytes {0} [built]
  [85] ./insta485/js/main.jsx 492 bytes {0} [built]
 [103] ./~/prop-types/index.js 1.4 kB {0} [built]
 [117] ./~/react-dom/lib/ReactDOM.js 5.19 kB {0} [built]
 [166] ./~/react-dom/lib/findDOMNode.js 2.46 kB {0} [built]
 [177] ./~/react/lib/ReactDOMFactories.js 5.48 kB {0} [built]
 [179] ./~/react/lib/ReactPropTypes.js 500 bytes {0} [built]
 [181] ./~/react/lib/ReactVersion.js 350 bytes {0} [built]
 [183] ./~/react/lib/createClass.js 688 bytes {0} [built]
    + 172 hidden modules
</span><span class="gp">$</span> ./node_modules/.bin/webpack <span class="nt">--watch</span>  <span class="c"># optional, for auto rebuild during development</span>
</code></pre></div></div>

<p>What just happened?  All the libraries needed by our JavaScript app were concatenated into <code class="highlighter-rouge">bundle.js</code>.  Then, it was transpiled to ES5, which is supported by all modern web browsers.</p>

<p>Fire up your web server again.  You can see that our <code class="highlighter-rouge">bundle.js</code> is available.</p>
<div class="language-console highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="gp">$</span> ./bin/insta485run
<span class="gp">$</span> curl <span class="nt">-s</span> <span class="s2">"http://localhost:8000/static/js/bundle.js"</span> | <span class="nb">grep </span>Likes <span class="nt">-m1</span>
<span class="gp">var Likes = function (_React$</span>Component<span class="o">)</span> <span class="o">{</span>
</code></pre></div></div>

<p>Finally, browse to <a href="http://localhost:8000/">http://localhost:8000/</a>, where you’ll see the likes widget.</p>

  </body>
</html>
