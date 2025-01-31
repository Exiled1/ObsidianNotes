Windows Active Directory is basically a windows domain set up to create a centralized administration domain to handle the configuration of computers on a domain.

The main components to a windows domain are:
- Active Directory
- Domain Controller
	- This server runs the active directory

The **Active Directory Domain Service (AD DS)** is a service that holds the information about every object that exists in the network.
- We got Groups, Users, Machines, Printers, Shares, etc.

# Types of AD DS "Objects"

### Users
Users are one of the objects known as security principals, meaning that they can be authenticated by the domain and can be assigned privileges over resources like files or printers. 

Users can be used to represent two types of entities:

- **People:** users will generally represent persons in your organisation that need to access the network, like employees.
- **Services:** you can also define users to be used by services like IIS or MSSQL. Every single service requires a user to run, but service users are different from regular users as they will only have the privileges needed to run their specific service.

### Machines

Machines are another type of object within Active Directory;