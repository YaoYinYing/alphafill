AlphaFill
=========

**AlphaFill** is an algorithm based on sequence and structure similarity that “transplants”
missing compounds to the [AlphaFold models](https://alphafold.ebi.ac.uk/). By adding the molecular context to the protein structures, the
models can be more easily appreciated in terms of function and structure integrity.

Building
--------
In order to build alphafill, you need to have a modern C++ compiler (c++17), a recent version of [cmake](https://cmake.org/) and the following libraries installed:

- [Libzeep](https://github.com/mhekkel/libzeep) version 5.1.5 or higher
- [libcif++](https://github.com/PDB-REDO/libcifpp) version 3.0.0 or higher
- libpq, the PostgreSQL library
- [libpqxx](http://www.pqxx.org/) version 7.2 or higher

And then you also need [yarn](https://yarnpkg.com/) to package the data for the web interface. Additionally you need [mrc](https://github.com/mhekkel/mrc) to package all the runtime data into resources in the final excutable.

Once all the requirements are met, building is as simple as:

```
git clone https://github.com/PDB-REDO/alphafill
cd alphafill
yarn		# will fetch all node modules
mkdir build
cd build
cmake ..
cmake --build .
cmake --install .
```

Configuration
-------------

### alphafill

For alphafill you need a copy of either PDB-REDO or PDB in mmCIF format. You also need a FastA formatted file for all sequences in this databank and an af-ligands.cif file. The latter is supplied with the code. Using these, you can construct a pdb-id-list file using the command:

```
alphafill --pdb-dir=${PDB_DIR} --pdb-fasta=${PDB_FASTA} --prepare-pdb-list --output pdb-id-list.txt
```

The pdb-id-list file is optional, but speeds up the process considerably (it records PDB id's that can be skipped since they do not contain interesting ligands).

All basic option can be store in a file called alphafill.conf which can be located in either the current working directory or in the directory `.config` in your home directory.

Running alpafill is then as easy as:

```
alphafill /srv/data/afdb/cif/AF-XXX.cif.gz /srv/data/af-filled/AF-XXX.cif.gz
```

### af-filledd
Before running the web application, you need to create a PostgreSQL database first. The owner and name can then be recorded in a af-filledd.conf file similar to the config file of alphafill. The location of your filled structures should be recorded as well as the db-dir option.

The database can be filled with the `--rebuild-db` option. If you built the code with resources using [mrc](https://github.com/mhekkel/mrc) this will take care of setting up the tables as well, otherwise you have to create the tables running the db-schema.sql file.

After this setting up, you can start a web server using `af-filledd start`. Use `af-filledd status` to find the status of the server and `af-filled stop` to stop it again. In this case the af-filledd server is run as a daemon and log files will be written to /var/log/af-filledd/. You can also start with the extra --no-daemon option and then the server will run in the foreground.
