# NAME

gpgkeymon - a shell script to keep tabs on a set of gpg keys.

# VERSION

version 5.0.0

# DESCRIPTION

The primary purpose of this program is to monitor a set of keys and report on their expiration status.

A typical report looks like:

	Valid
	-----
	  B6042E2BD1FDBC2BCA8588B2FF8D3B45B7B875A9
	  Jean Paul Galea <jeanpaul@yubico.com>
	  expires on 2017-01-24

	Revoked: none

	Never expire: none

	Have expired: none

	Expire in 90 days: none

In batch mode you could also use the output as a source for other scripts (e.g. to email the key holder directly).

	2017-01-24:0:B6042E2BD1FDBC2BCA8588B2FF8D3B45B7B875A9:Jean Paul Galea:jeanpaul@yubico.com

# INSTALL

As root, install the script, create a custom user and switch to the new user.

	# wget -O/usr/local/bin/gpgkeymon -- \
	  'https://raw.githubusercontent.com/jeanpaulgalea/gpgkeymon/master/gpgkeymon'

	# chmod +x /usr/local/bin/gpgkeymon

	# adduser --system --home /home/gpgkeymon --shell /bin/sh \
	  --disabled-login --group --gecos '' "gpgkeymon"

	# su - gpgkeymon


# CONFIGURATION

gpgkeymon generates a report based on the keyring at ~/.gnupg or $GNUPGHOME if set.

As the new user, you can import keys from a keyserver:

	$ gpg --keyserver pgp.mit.edu --recv B6042E2BD1FDBC2BCA8588B2FF8D3B45B7B875A9

Or import from file:

	$ gpg --import pubkey.gpg

# EXAMPLES

Generate the default report.

	$ gpgkeymon

Warn one month before the keys expire (rather than the default 3 months).

	$ gpgkeymon -w 30

Only interested in keys without any expiry date (top 100).

	$ gpgkeymon -a 0 --never-expire 100

Only revoked and expired keys (top 100).

	$ gpgkeymon -a 0 --revoked 100 --expired 100

Same but scriptable.

	$ gpgkeymon -a 0 --revoked 100 --expired 100 -b

Show me everything except keys which are in good state.

	$ gpgkeymon --valid 0

Don't hide any section but limit each one to top 100 keys.

	$ gpgkeymon -a 100

Use a different gpg binary.

	$ gpgkeymon --gpg /mnt/usr/bin/gpg2

Use a different keyring.

	$ GNUPGHOME=/path/to/keyring gpgkeymon

To email a weekly report (assuming a working mail setup), create a file at `/etc/cron.d/gpgkeymon` with the following:

	SHELL=/bin/sh
	0 6 * * 1  gpgkeymon /usr/local/bin/gpgkeymon | /usr/bin/mailx -s "gpgkeymon weekly report" admin@example.org
