# Pagination-Product-Search-using-node-js
const Product = require('../models/Product');

// GET /api/products?search=shoe&category=men&page=1&limit=10
exports.getProducts = async (req, res) => {
  try {
    const { search = '', category, page = 1, limit = 10 } = req.query;

    // Build filter based on search and category
    const filter = {};
    if (search) {
      filter.name = { $regex: search, $options: 'i' };
    }
    if (category) {
      filter.category = { $regex: category, $options: 'i' };
    }

    const products = await Product.find(filter)
      .skip((page - 1) * limit)
      .limit(parseInt(limit));

    const total = await Product.countDocuments(filter);

    res.json({
      page: parseInt(page),
      limit: parseInt(limit),
      totalPages: Math.ceil(total / limit),
      totalItems: total,
      products
    });
  } catch (err) {
    res.status(500).json({ msg: 'Server Error', error: err.message });
  }
};
