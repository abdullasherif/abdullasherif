import React, { useState, useEffect } from 'react';

function App() {
  const [prices, setPrices] = useState({});
  const [watchlist, setWatchlist] = useState(['bitcoin', 'ethereum']); // User's watchlist

  useEffect(() => {
    const fetchPrices = async () => {
      const ids = watchlist.join(',');
      const response = await fetch(`https://api.coingecko.com/api/v3/simple/price?ids=${ids}&vs_currencies=usd`);
      const data = await response.json();
      setPrices(data);
    };

    fetchPrices();
    const intervalId = setInterval(fetchPrices, 5000);
    return () => clearInterval(intervalId);
  }, [watchlist]); // Re-fetch when watchlist changes

  const handleAddToWatchlist = (coinId) => {
    if (!watchlist.includes(coinId)) {
      setWatchlist([...watchlist, coinId]);
    }
  };

  const handleRemoveFromWatchlist = (coinId) => {
    setWatchlist(watchlist.filter(id => id !== coinId));
  };

  return (
    <div>
      <h1>Crypto Watchlist</h1>
      <ul>
        {watchlist.map(coinId => (
          <li key={coinId}>
            {coinId}: {prices[coinId] ? `$${prices[coinId].usd}` : 'Loading...'}
            <button onClick={() => handleRemoveFromWatchlist(coinId)}>Remove</button>
          </li>
        ))}
      </ul>

      <div>
        <input type="text" placeholder="Enter coin ID (e.g., litecoin)" />
        <button onClick={() => handleAddToWatchlist(coinId)}>Add to Watchlist</button>
      </div>
    </div>
  );
}

export default App;
