Getting started with FOF
------------------------

### Download and install FOF

You can download FOF as an installable Joomla! library package from [our
repository](https://www.akeebabackup.com/download/fof.html). You can
install it like any other extension under Joomla! 2.x and later.

**Using the latest development version**

You can clone a read-only copy of the Git repository of FOF in your
local machine. Make sure you symlink or copy the fof directory to your
dev site's libraries/fof directory. Alternatively, we publish dev
releases in the [dev release
repository](https://www.akeebabackup.com/download/fof-dev.html). These
are installable packages but please note that they may be out of date
compared to the Git HEAD. Dev releases are not published automatically
and may be several revisions behind the current Git master branch.

### Using it in your extension

The recommended method for including FOF in your component, module or
plugin is using this short code snippet right after your
defined('\_JEXEC') or die() statement (Joomla! 2.x and later):

    if (!defined('FOF_INCLUDED')) {
        include_once JPATH_LIBRARIES . '/fof/include.php';
    }

Alternatively, you can use the one-liner:

    require_once JPATH_LIBRARIES . '/fof/include.php';

From that point onwards you can use FOF in your extension.

### Installing FOF with your component

Unfortunately, Joomla! doesn't allow us to version checking before
installing a library package. This means that it's your responsibility
to check that there is no newer version of FOF installed in the user's
site before attempting to install FOF with your extension. In the
following paragraphs we are going to demonstrate one way to do that for
Joomla! 2.x / 3.x component packages.

Include a directory called fof in your installation package. The
directory should contain the files of the installation package's fof
directory. Then, in your script.mycomponent.php file add the following
method:

    /**
     * Check if FoF is already installed and install if not
     *
     * @param   object  $parent  class calling this method
     *
     * @return  array            Array with performed actions summary
     */
    private function _installFOF($parent)
    {
        $src = $parent->getParent()->getPath('source');

        // Load dependencies
        JLoader::import('joomla.filesystem.file');
        JLoader::import('joomla.utilities.date');
        $source = $src . '/fof';

        if (!defined('JPATH_LIBRARIES'))
        {
            $target = JPATH_ROOT . '/libraries/fof';
        }
        else
        {
            $target = JPATH_LIBRARIES . '/fof';
        }
        $haveToInstallFOF = false;

        if (!is_dir($target))
        {
            $haveToInstallFOF = true;
        }
        else
        {
            $fofVersion = array();

            if (file_exists($target . '/version.txt'))
            {
                $rawData = JFile::read($target . '/version.txt');
                $info    = explode("\n", $rawData);
                $fofVersion['installed'] = array(
                    'version'   => trim($info[0]),
                    'date'      => new JDate(trim($info[1]))
                );
            }
            else
            {
                $fofVersion['installed'] = array(
                    'version'   => '0.0',
                    'date'      => new JDate('2011-01-01')
                );
            }

            $rawData = JFile::read($source . '/version.txt');
            $info    = explode("\n", $rawData);
            $fofVersion['package'] = array(
                'version'   => trim($info[0]),
                'date'      => new JDate(trim($info[1]))
            );

            $haveToInstallFOF = $fofVersion['package']['date']->toUNIX() > $fofVersion['installed']['date']->toUNIX();
        }

        $installedFOF = false;

        if ($haveToInstallFOF)
        {
            $versionSource = 'package';
            $installer = new JInstaller;
            $installedFOF = $installer->install($source);
        }
        else
        {
            $versionSource = 'installed';
        }

        if (!isset($fofVersion))
        {
            $fofVersion = array();

            if (file_exists($target . '/version.txt'))
            {
                $rawData = JFile::read($target . '/version.txt');
                $info    = explode("\n", $rawData);
                $fofVersion['installed'] = array(
                    'version'   => trim($info[0]),
                    'date'      => new JDate(trim($info[1]))
                );
            }
            else
            {
                $fofVersion['installed'] = array(
                    'version'   => '0.0',
                    'date'      => new JDate('2011-01-01')
                );
            }

            $rawData = JFile::read($source . '/version.txt');
            $info    = explode("\n", $rawData);
            $fofVersion['package'] = array(
                'version'   => trim($info[0]),
                'date'      => new JDate(trim($info[1]))
            );
            $versionSource = 'installed';
        }

        if (!($fofVersion[$versionSource]['date'] instanceof JDate))
        {
            $fofVersion[$versionSource]['date'] = new JDate;
        }

        return array(
            'required'  => $haveToInstallFOF,
            'installed' => $installedFOF,
            'version'   => $fofVersion[$versionSource]['version'],
            'date'      => $fofVersion[$versionSource]['date']->format('Y-m-d'),
        );
    }

You need to call it from inside your postflight() method. For example:

    /**
     * Method to run after an install/update/uninstall method
     *
     * @param   object  $type    type of change (install, update or discover_install)
     * @param   object  $parent  class calling this method
     *
     * @return void
     */
    function postflight($type, $parent)
    {
            $fofInstallationStatus = $this->_installFOF($parent);
    }

> **Important**
>
> Due to a bug/feature in Joomla! 1.6 and later, your component's
> manifest file must start with a letter before L, otherwise Joomla!
> will assume that lib\_fof.xml is your extension's XML manifest and
> install FOF instead of your extension. We suggest using the
> com\_yourComponentName.xml convention, e.g. com\_todo.xml. There is a
> patch pending in Joomla!'s tracker for this issue, hopefully it will
> be accepted sometime soon.

### Sample applications

FOF comes with two sample applications which are used to demonstrate its
features, [To-Do](https://github.com/akeeba/todo-fof-example) and
[Contact Us](https://github.com/akeeba/contactus). These were conceived
and developed in different points of FOF's development. As a result they
are always in a state of flux and will definitely not expose all of
FOF's features.

Another good way to learn some FOF tricks is by reading the source code
of existing FOF-based components. Just remember that we are all real
world developers and sometimes our code is anything but kosher ;)