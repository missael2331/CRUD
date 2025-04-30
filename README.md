
// db.js
const { Sequelize } = require('sequelize');

const sequelize = new Sequelize({
    dialect: 'sqlite',
    storage: './database.sqlite'
});

module.exports = sequelize;
// models/user.js
const { DataTypes } = require('sequelize');
const sequelize = require('../db');

const User = sequelize.define('User', {
    nombre: {
        type: DataTypes.STRING,
        allowNull: false
    },
    edad: {
        type: DataTypes.INTEGER,
        allowNull: false
    }
});

module.exports = User;
 // models/user.js
const { DataTypes } = require('sequelize');
const sequelize = require('../db');

const User = sequelize.define('User', {
    nombre: {
        type: DataTypes.STRING,
        allowNull: false
    },
    edad: {
        type: DataTypes.INTEGER,
        allowNull: false
    }
});

module.exports = User;
// controllers/userController.js
const User = require('../models/user');

exports.getAllUsers = async (req, res) => {
    const users = await User.findAll();
    res.json(users);
};

exports.getUserById = async (req, res) => {
    const user = await User.findByPk(req.params.id);
    user ? res.json(user) : res.status(404).json({ message: 'Usuario no encontrado' });
};

exports.createUser = async (req, res) => {
    const { nombre, edad } = req.body;
    try {
        const user = await User.create({ nombre, edad });
        res.status(201).json(user);
    } catch (err) {
        res.status(400).json({ message: 'Error al crear usuario', error: err.message });
    }
};

exports.updateUser = async (req, res) => {
    const user = await User.findByPk(req.params.id);
    if (!user) return res.status(404).json({ message: 'Usuario no encontrado' });

    const { nombre, edad } = req.body;
    await user.update({ nombre, edad });
    res.json(user);
};

exports.deleteUser = async (req, res) => {
    const user = await User.findByPk(req.params.id);
    if (!user) return res.status(404).json({ message: 'Usuario no encontrado' });

    await user.destroy();
    res.json({ message: 'Usuario eliminado' });
};
// routes/userRoutes.js
const express = require('express');
const router = express.Router();
const userController = require('../controllers/userController');

router.get('/', userController.getAllUsers);
router.get('/:id', userController.getUserById);
router.post('/', userController.createUser);
router.put('/:id', userController.updateUser);
router.delete('/:id', userController.deleteUser);

module.exports = router;
// server.js
const express = require('express');
const sequelize = require('./db');
const userRoutes = require('./routes/userRoutes');

const app = express();
const PORT = 3000;

app.use(express.json());
app.use('/api/usuarios', userRoutes);

(async () => {
    try {
        await sequelize.sync({ force: false });
        app.listen(PORT, () => console.log(`Servidor corriendo en http://localhost:${PORT}`));
    } catch (error) {
        console.error('Error al conectar a la base de datos:', error);
    }
})();
