# NAME

gpgkeymon - a shell script to keep tabs on a set of gpg keys.

# VERSION

version 4.0.0

# DESCRIPTION

The primary purpose of this program is to monitor a set of keys and report on their expiration status.

It can be configured to mail a weekly/monthly report to a system administrator or as a source for other scripts, e.g. to email the key holder directly or to even submit checks to a monitoring system.

# CONFIGURATION

Without any configuration `gpgkeymon` generates a report based on the local keyring.

However if `~/.gpgkeymon` is found, the local keyring is ignored and `~/.gpgkeymon` is used instead.

Here's the exact lookup process:

	1. ~/.gpgkeymon if directory is found.

	2. $GNUPGHOME if exported.

	3. ~/.gnupg if directory is found.

The `~/.gpgkeymon` directory can be configured to generate a report by a list of key fingerprints, or public key files, or both.

To use fingerprints (automatically fetches the key from a keyserver):

	mkdir -p ~/.gpgkeymon
	touch ~/.gpgkeymon/fingerprints
	echo 75DDC3C4A499F1A18CB5F3C8CBF8D6FD518E17E1 >> ~/.gpgkeymon/fingerprints
	echo 790BC7277767219C42C86F933B4FE6ACC0B21F32 >> ~/.gpgkeymon/fingerprints

To use public key files:

	mkdir -p ~/.gpgkeymon/pubkeys

	gpg --export ABCDEF01 > ~/.gpgkeymon/pubkeys/nibbles.asc

	# subfolder as a group
	mkdir -p /etc/gpgkeymon/pubkeys/sysadmins
	gpg --export ABCDEF02 > ~/.gpgkeymon/pubkeys/sysadmins/tom.asc
	gpg --export ABCDEF03 > ~/.gpgkeymon/pubkeys/sysadmins/jerry.asc

	# another group
	mkdir -p ~/.gpgkeymon/pubkeys/developers
	gpg --export ABCDEF04 > ~/.gpgkeymon/pubkeys/developers/spike.asc
	gpg --export ABCDEF05 > ~/.gpgkeymon/pubkeys/developers/tyke.asc


# EXAMPLES

	# use a different gpg binary
	gpgkeymon --gpg /mnt/usr/bin/gpg2

	# use a preferred keyserver when fetching keys by fingerprint
	gpgkeymon --keyserver myhost.mydomain.org

	# warn one month before keys expire (rather than the default 3 months)
	gpgkeymon -w 30

	# only interested in keys without any expiry date (top 100)
	gpgkeymon -a 0 --never-expire 100

	# only revoked and expired keys (top 100)
	gpgkeymon -a 0 --revoked 100 --expired 100

	# same but scriptable
	gpgkeymon -a 0 --revoked 100 --expired 100 -b

	# show me everything except keys which are in good state
	gpgkeymon --valid 0

	# don't hide any section but limit each one to top 100 keys
	gpgkeymon -a 100
