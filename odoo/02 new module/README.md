## How to Set Up a New Odoo Module

### 1. **Creating a New Module**

To create a new module in Odoo, follow these steps:

1. Navigate to the `addons` directory of your Odoo installation.
   ```bash
   cd /path/to/odoo/addons
   ```
2. Create a new directory for your module.
   ```bash
   mkdir my_module
   cd my_module
   ```

3. Your module folder will follow a specific structure, which we’ll explain below.

---

### 2. **Odoo Module File Structure**

The basic structure of an Odoo module contains the following files:

```
my_module/
│
├── __init__.py
├── __manifest__.py
├── models/
│   ├── __init__.py
│   └── my_model.py
├── views/
│   └── my_model_views.xml
├── security/
│   └── ir.model.access.csv
├── data/
│   └── my_model_data.xml
└── static/
    └── description/
        └── icon.png
```

Let’s break down the role of each file and its content.

---

### 3. **Essential Files in an Odoo Module**

#### **1. `__init__.py`** 
This file initializes the module. It imports the Python files in the module.

**Example:**

```python
# __init__.py
from . import models
```

---

#### **2. `__manifest__.py`** [->](#link_manifest)
This file defines the module's metadata, dependencies, and Odoo version compatibility. It’s essential for registering the module within Odoo.

**Example:**

```python
# __manifest__.py
{
    'name': 'My Custom Module',
    'version': '1.0',
    'summary': 'A description of my custom module',
    'description': """
        Detailed description of the module.
    """,
    'author': 'Your Name',
    'depends': ['base'],  # Dependencies for this module
    'data': [
        'security/ir.model.access.csv',
        'views/my_model_views.xml',
        'data/my_model_data.xml',
    ],
    'installable': True,
    'auto_install': False,
}
```

---

### 4. **Model Definition**

#### **3. `models/` Directory**
This folder holds Python files that define your business logic (models, methods, etc.).

#### **4. `my_model.py`**
This is the file where you define your custom models, fields, and business logic. Each model typically corresponds to a database table.

**Example:**

```python
# models/my_model.py
from odoo import models, fields

class MyModel(models.Model):
    _name = 'my.model'  # Define the model name
    _description = 'My Model Description'

    name = fields.Char(string='Name', required=True)
    description = fields.Text(string='Description')
    active = fields.Boolean(string='Active', default=True)
```

- `_name`: The unique name of the model, which defines the corresponding database table.
- `fields.Char`: Defines a character field (e.g., name).
- `fields.Text`: Defines a text field.
- `fields.Boolean`: Defines a boolean field.

---

### 5. **Views and UI**

#### **5. `views/` Directory**
This directory contains XML files defining how the user interface will look (form views, tree views, etc.).

#### **6. `my_model_views.xml`**
This file defines the views (how the model data is displayed in the Odoo interface).

**Example:**

```xml
<!-- views/my_model_views.xml -->
<odoo>
    <record id="view_my_model_form" model="ir.ui.view">
        <field name="name">my.model.form</field>
        <field name="model">my.model</field>
        <field name="arch" type="xml">
            <form string="My Model">
                <sheet>
                    <group>
                        <field name="name"/>
                        <field name="description"/>
                        <field name="active"/>
                    </group>
                </sheet>
            </form>
        </field>
    </record>

    <record id="view_my_model_tree" model="ir.ui.view">
        <field name="name">my.model.tree</field>
        <field name="model">my.model</field>
        <field name="arch" type="xml">
            <tree string="My Model">
                <field name="name"/>
                <field name="active"/>
            </tree>
        </field>
    </record>
</odoo>
```

---

### 6. **Security**

#### **7. `security/ir.model.access.csv`**
This file manages access rights for the new model. It ensures that only the right users can read, write, create, or delete records from the model.

**Example:**

```csv
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_my_model_user,my.model,model_my_model,base.group_user,1,1,1,0
```

- `perm_read`: Allows reading records.
- `perm_write`: Allows modifying records.
- `perm_create`: Allows creating new records.
- `perm_unlink`: Allows deleting records.

---

### 7. **Static Assets**

#### **8. `static/description/icon.png`**
This directory contains static files like images and CSS. The `icon.png` file is used to display an icon for your module in the Odoo apps menu.

---

### 8. **Data Files**

#### **9. `data/` Directory**
This directory holds initial data for your module. You can predefine data such as configurations, settings, and default values.

#### **10. `my_model_data.xml`**
This file is used to populate initial data when the module is installed.

**Example:**

```xml
<!-- data/my_model_data.xml -->
<odoo>
    <record id="my_model_data_1" model="my.model">
        <field name="name">Sample Data 1</field>
        <field name="description">This is a sample data record.</field>
        <field name="active">True</field>
    </record>

    <record id="my_model_data_2" model="my.model">
        <field name="name">Sample Data 2</field>
        <field name="description">Another example data record.</field>
        <field name="active">False</field>
    </record>
</odoo>
```

---

### 9. **Running the Module**

1. **Restart Odoo**:
   After creating the module, restart your Odoo server:
   ```bash
   ./odoo-bin -c /etc/odoo/odoo.conf
   ```

2. **Install the Module**:
   Go to the Odoo apps menu, search for your module, and install it.

---
---
---

## link_manifest

#### **What is `__manifest__.py`?**

The `__manifest__.py` file in an Odoo module is crucial as it defines the metadata of the module. This file is used by Odoo to register and load the module during installation. It describes the module, its dependencies, data files, and various configurations that Odoo uses to manage it.

In simpler terms, it's like a configuration file that tells Odoo how to handle the module.

#### **Why is `__manifest__.py` used?**

- It provides metadata such as the module's name, version, author, and description.
- It defines the module's dependencies (other Odoo modules that need to be installed).
- It lists the data files (like views, security, and configurations) to be loaded during the module's installation.
- It specifies whether the module can be installed, auto-installed, and more.

---

### **Minimum Values in `__manifest__.py`**

At a minimum, the `__manifest__.py` file should include the following:

- **`name`**: The name of the module.
- **`version`**: The version of the module.
- **`depends`**: A list of modules that your module depends on.
- **`data`**: A list of XML/CSV files that need to be loaded during installation.
- **`installable`**: Whether the module can be installed or not.

**Example of Minimum Values:**

```python
{
    'name': 'My Custom Module',
    'version': '1.0',
    'depends': ['base'],
    'data': [],
    'installable': True,
}
```

---

### **All Possible Values in `__manifest__.py`**

Here are all the common fields you can define in the `__manifest__.py` file:

1. **`name`**: The name of the module.
   - **Type**: String
   - **Example**:
     ```python
     'name': 'Sales Management',
     ```

2. **`version`**: The version number of the module.
   - **Type**: String
   - **Example**:
     ```python
     'version': '1.0',
     ```

3. **`summary`**: A short summary or tagline for the module.
   - **Type**: String
   - **Example**:
     ```python
     'summary': 'Manage sales orders and customers',
     ```

4. **`description`**: A more detailed description of the module.
   - **Type**: String (or multi-line string for longer descriptions)
   - **Example**:
     ```python
     'description': """
     This module allows you to manage sales, customers, and sales orders.
     """,
     ```

5. **`author`**: The name of the author or company that developed the module.
   - **Type**: String
   - **Example**:
     ```python
     'author': 'Your Name or Company',
     ```

6. **`category`**: The category under which the module falls (like Accounting, Sales, etc.).
   - **Type**: String
   - **Example**:
     ```python
     'category': 'Sales',
     ```

7. **`depends`**: A list of other Odoo modules that this module depends on.
   - **Type**: List of strings
   - **Example**:
     ```python
     'depends': ['base', 'sale'],
     ```

8. **`data`**: A list of data files (XML/CSV) to be loaded when the module is installed.
   - **Type**: List of strings (paths to XML/CSV files)
   - **Example**:
     ```python
     'data': [
         'views/sale_order_views.xml',
         'security/ir.model.access.csv',
     ],
     ```

9. **`demo`**: A list of demo data files to be loaded in demonstration mode.
   - **Type**: List of strings
   - **Example**:
     ```python
     'demo': [
         'demo/demo_data.xml',
     ],
     ```

10. **`qweb`**: A list of QWeb template files (used for web and report views).
    - **Type**: List of strings
    - **Example**:
      ```python
      'qweb': ['static/src/xml/my_template.xml'],
      ```

11. **`installable`**: Indicates whether the module can be installed.
    - **Type**: Boolean
    - **Example**:
      ```python
      'installable': True,
      ```

12. **`auto_install`**: Indicates whether the module should be automatically installed if all its dependencies are installed.
    - **Type**: Boolean
    - **Example**:
      ```python
      'auto_install': False,
      ```

13. **`application`**: Indicates whether the module is a standalone application (shows up in the app menu).
    - **Type**: Boolean
    - **Example**:
      ```python
      'application': True,
      ```

14. **`license`**: Specifies the license under which the module is released (e.g., AGPL-3, LGPL-3, MIT, etc.).
    - **Type**: String
    - **Example**:
      ```python
      'license': 'LGPL-3',
      ```

15. **`website`**: The URL of the website related to the module or its author.
    - **Type**: String
    - **Example**:
      ```python
      'website': 'https://yourwebsite.com',
      ```

16. **`maintainers`**: List of maintainers for the module.
    - **Type**: List of strings
    - **Example**:
      ```python
      'maintainers': ['your_username'],
      ```

17. **`external_dependencies`**: Lists Python libraries or external binaries that are required for the module to function.
    - **Type**: Dictionary
    - **Example**:
      ```python
      'external_dependencies': {
          'python': ['pandas', 'numpy'],
          'bin': ['wkhtmltopdf'],
      },
      ```

18. **`post_init_hook`**: Defines a function that will run after the module is installed.
    - **Type**: String (the function name)
    - **Example**:
      ```python
      'post_init_hook': 'post_install_function',
      ```

---

### Example `__manifest__.py` with All Possible Values

```python
{
    'name': 'Sales Management',
    'version': '1.2.0',
    'summary': 'Manage sales orders and customers',
    'description': """
        This module allows you to manage sales, customers, and sales orders.
        You can create quotes, convert them into sales orders, and track delivery.
    """,
    'author': 'Your Company Name',
    'website': 'https://yourcompany.com',
    'license': 'LGPL-3',
    'category': 'Sales',
    'depends': ['base', 'sale'],
    'data': [
        'views/sale_order_views.xml',
        'security/ir.model.access.csv',
        'data/sale_order_data.xml',
    ],
    'demo': [
        'demo/demo_data.xml',
    ],
    'qweb': [
        'static/src/xml/my_template.xml',
    ],
    'installable': True,
    'auto_install': False,
    'application': True,
    'maintainers': ['your_username'],
    'external_dependencies': {
        'python': ['pandas'],
        'bin': ['wkhtmltopdf'],
    },
    'post_init_hook': 'post_install_function',
}
```
---
---

