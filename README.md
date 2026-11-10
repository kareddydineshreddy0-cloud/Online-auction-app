# Online-auction-app
An online auction application where users can view products, place bids, and manage auctions online.

import 'dart:async';
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';
import 'package:intl/intl.dart';

void main() {
  runApp(const AuctionApp());
}

class AuctionApp extends StatelessWidget {
  const AuctionApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Auction Hub',
      theme: ThemeData(
        colorSchemeSeed: const Color(0xFF6558E8),
        scaffoldBackgroundColor: const Color(0xFFF6F6FA),
        textTheme: GoogleFonts.poppinsTextTheme(),
        useMaterial3: true,
      ),
      home: const HomeScreen(),
    );
  }
}

class AuctionProduct {
  final String name;
  final String image;
  final double startingPrice;
  double currentBid;
  final String description;
  final DateTime endTime;

  AuctionProduct({
    required this.name,
    required this.image,
    required this.startingPrice,
    required this.currentBid,
    required this.description,
    required this.endTime,
  });
}

final List<AuctionProduct> products = [
  AuctionProduct(
    name: 'Luxury Watch',
    image: 'https://images.unsplash.com/photo-1524592094714-0f0654e20314?w=600',
    startingPrice: 5000,
    currentBid: 7500,
    description: 'Premium luxury watch with elegant design.',
    endTime: DateTime.now().add(const Duration(hours: 2)),
  ),
  AuctionProduct(
    name: 'Sports Car',
    image: 'https://images.unsplash.com/photo-1503376780353-7e6692767b70?w=600',
    startingPrice: 500000,
    currentBid: 650000,
    description: 'Premium sports car in excellent condition.',
    endTime: DateTime.now().add(const Duration(hours: 5)),
  ),
  AuctionProduct(
    name: 'Modern Camera',
    image: 'https://images.unsplash.com/photo-1516035069371-29a1b244cc32?w=600',
    startingPrice: 15000,
    currentBid: 18000,
    description: 'Professional camera for photography lovers.',
    endTime: DateTime.now().add(const Duration(hours: 3)),
  ),
];

class HomeScreen extends StatefulWidget {
  const HomeScreen({super.key});

  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  int selectedIndex = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text(
          'Auction Hub',
          style: TextStyle(fontWeight: FontWeight.bold),
        ),
        actions: [
          IconButton(
            icon: const Icon(Icons.notifications_outlined),
            onPressed: () {},
          ),
        ],
      ),
      body: selectedIndex == 0
          ? auctionHome()
          : Center(
              child: Text(
                selectedIndex == 1 ? 'My Auctions' : 'My Profile',
                style: const TextStyle(fontSize: 24),
              ),
            ),
      bottomNavigationBar: NavigationBar(
        selectedIndex: selectedIndex,
        onDestinationSelected: (index) {
          setState(() => selectedIndex = index);
        },
        destinations: const [
          NavigationDestination(
            icon: Icon(Icons.home_outlined),
            label: 'Home',
          ),
          NavigationDestination(
            icon: Icon(Icons.gavel),
            label: 'My Auctions',
          ),
          NavigationDestination(
            icon: Icon(Icons.person_outline),
            label: 'Profile',
          ),
        ],
      ),
    );
  }

  Widget auctionHome() {
    return ListView(
      padding: const EdgeInsets.all(16),
      children: [
        const Text(
          'Find Your Next Deal',
          style: TextStyle(fontSize: 25, fontWeight: FontWeight.bold),
        ),
        const SizedBox(height: 8),
        const Text(
          'Bid on exclusive products and win amazing deals.',
          style: TextStyle(color: Colors.grey),
        ),
        const SizedBox(height: 20),
        TextField(
          decoration: InputDecoration(
            hintText: 'Search auctions...',
            prefixIcon: const Icon(Icons.search),
            filled: true,
            fillColor: Colors.white,
            border: OutlineInputBorder(
              borderRadius: BorderRadius.circular(15),
              borderSide: BorderSide.none,
            ),
          ),
        ),
        const SizedBox(height: 25),
        const Text(
          'Live Auctions',
          style: TextStyle(fontSize: 21, fontWeight: FontWeight.bold),
        ),
        const SizedBox(height: 15),
        ...products.map((product) => ProductCard(product: product)),
      ],
    );
  }
}

class ProductCard extends StatelessWidget {
  final AuctionProduct product;

  const ProductCard({super.key, required this.product});

  @override
  Widget build(BuildContext context) {
    return Card(
      color: Colors.white,
      margin: const EdgeInsets.only(bottom: 18),
      clipBehavior: Clip.antiAlias,
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(18),
      ),
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Image.network(
            product.image,
            height: 190,
            width: double.infinity,
            fit: BoxFit.cover,
            errorBuilder: (_, __, ___) =>
                const SizedBox(height: 190, child: Icon(Icons.image)),
          ),
          Padding(
            padding: const EdgeInsets.all(15),
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text(
                  product.name,
                  style: const TextStyle(
                    fontSize: 19,
                    fontWeight: FontWeight.bold,
                  ),
                ),
                const SizedBox(height: 8),
                Text(
                  'Current Bid: ${NumberFormat.currency(locale: 'en_IN', symbol: '₹', decimalDigits: 0).format(product.currentBid)}',
                  style: const TextStyle(
                    color: Color(0xFF6558E8),
                    fontSize: 17,
                    fontWeight: FontWeight.bold,
                  ),
                ),
                const SizedBox(height: 12),
                Row(
                  mainAxisAlignment: MainAxisAlignment.spaceBetween,
                  children: [
                    const Text('Live auction'),
                    ElevatedButton(
                      onPressed: () {
                        Navigator.push(
                          context,
                          MaterialPageRoute(
                            builder: (_) =>
                                AuctionDetails(product: product),
                          ),
                        );
                      },
                      child: const Text('Place Bid'),
                    ),
                  ],
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }
}

class AuctionDetails extends StatefulWidget {
  final AuctionProduct product;

  const AuctionDetails({super.key, required this.product});

  @override
  State<AuctionDetails> createState() => _AuctionDetailsState();
}

class _AuctionDetailsState extends State<AuctionDetails> {
  late Timer timer;
  late Duration remaining;
  double bidAmount = 0;

  @override
  void initState() {
    super.initState();
    bidAmount = widget.product.currentBid + 500;
    remaining = widget.product.endTime.difference(DateTime.now());

    timer = Timer.periodic(const Duration(seconds: 1), (_) {
      if (!mounted) return;
      setState(() {
        remaining = widget.product.endTime.difference(DateTime.now());
      });
    });
  }

  @override
  void dispose() {
    timer.cancel();
    super.dispose();
  }

  String get countdown {
    if (remaining.isNegative) return 'Auction Ended';
    final hours = remaining.inHours;
    final minutes = remaining.inMinutes % 60;
    final seconds = remaining.inSeconds % 60;
    return '${hours}h ${minutes}m ${seconds}s';
  }

  void placeBid() {
    if (remaining.isNegative) {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(content: Text('Auction has ended')),
      );
      return;
    }

    setState(() {
      widget.product.currentBid = bidAmount;
      bidAmount += 500;
    });

    ScaffoldMessenger.of(context).showSnackBar(
      const SnackBar(content: Text('Bid placed successfully!')),
    );
  }

  @override
  Widget build(BuildContext context) {
    final product = widget.product;

    return Scaffold(
      appBar: AppBar(title: const Text('Auction Details')),
      body: ListView(
        padding: const EdgeInsets.all(18),
        children: [
          ClipRRect(
            borderRadius: BorderRadius.circular(18),
            child: Image.network(
              product.image,
              height: 260,
              fit: BoxFit.cover,
            ),
          ),
          const SizedBox(height: 20),
          Text(
            product.name,
            style: const TextStyle(
              fontSize: 25,
              fontWeight: FontWeight.bold,
            ),
          ),
          const SizedBox(height: 10),
          Text(product.description),
          const SizedBox(height: 20),
          Card(
            child: Padding(
              padding: const EdgeInsets.all(18),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  const Text('Current Highest Bid'),
                  Text(
                    NumberFormat.currency(
                      locale: 'en_IN',
                      symbol: '₹',
                      decimalDigits: 0,
                    ).format(product.currentBid),
                    style: const TextStyle(
                      fontSize: 28,
                      color: Color(0xFF6558E8),
                      fontWeight: FontWeight.bold,
                    ),
                  ),
                  const SizedBox(height: 15),
                  const Text('Time Remaining'),
                  Text(
                    countdown,
                    style: const TextStyle(
                      fontSize: 20,
                      fontWeight: FontWeight.bold,
                    ),
                  ),
                ],
              ),
            ),
          ),
          const SizedBox(height: 20),
          const Text(
            'Your Bid Amount',
            style: TextStyle(fontWeight: FontWeight.bold),
          ),
          const SizedBox(height: 10),
          Text(
            NumberFormat.currency(
              locale: 'en_IN',
              symbol: '₹',
              decimalDigits: 0,
            ).format(bidAmount),
            style: const TextStyle(fontSize: 24),
          ),
          Slider(
            value: bidAmount.clamp(
              product.currentBid + 500,
              product.currentBid + 50000,
            ),
            min: product.currentBid + 500,
            max: product.currentBid + 50000,
            divisions: 99,
            onChanged: (value) {
              setState(() => bidAmount = value);
            },
          ),
          const SizedBox(height: 20),
          SizedBox(
            height: 52,
            child: ElevatedButton.icon(
              onPressed: remaining.isNegative ? null : placeBid,
              icon: const Icon(Icons.gavel),
              label: const Text(
                'Place Your Bid',
                style: TextStyle(fontSize: 17),
              ),
            ),
          ),
        ],
      ),
    );
  }
}
