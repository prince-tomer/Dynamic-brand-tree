# Dynamic-brand-tree
import { BrowserRouter as Router, Route, Routes } from 'react-router-dom';
import React, { useState } from 'react';
import { Card } from '@/components/ui/card';
import { Button } from '@/components/ui/button';

const BrandTreeComponent = () => {
  const [expandedBrand, setExpandedBrand] = useState(null);
  const brands = [
    {
      name: 'Brand 1',
      subsidiaries: ['Sub 1-1', 'Sub 1-2', 'Sub 1-3']
    },
    {
      name: 'Brand 2',
      subsidiaries: ['Sub 2-1', 'Sub 2-2', 'Sub 2-3']
    },
    {
      name: 'Brand 3',
      subsidiaries: ['Sub 3-1', 'Sub 3-2', 'Sub 3-3']
    }
  ];

  const toggleExpand = (brandName) => {
    setExpandedBrand(expandedBrand === brandName ? null : brandName);
  };

  const getAnimationClass = (brandName) => {
    return expandedBrand === brandName ? 'max-h-screen transition-max-h duration-500 ease-in' : 'max-h-0 overflow-hidden transition-max-h duration-500 ease-out';
  };

  return (
    <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
      {brands.map((brand) => (
        <Card key={brand.name} className="p-4">
          <h2 className="text-xl font-bold mb-2">{brand.name}</h2>
          <Button onClick={() => toggleExpand(brand.name)}>
            {expandedBrand === brand.name ? 'Collapse' : 'Expand'}
          </Button>
          <ul className={`mt-2 ${getAnimationClass(brand.name)}`}>
            {expandedBrand === brand.name && brand.subsidiaries.map((sub) => (
              <li key={sub} className="flex justify-between items-center">
                {sub}
                <Button className="ml-4" onClick={() => window.location.href = `/brand-details?sub=${encodeURIComponent(sub)}`}>Write a Review</Button>
              </li>
            ))}
          </ul>
        </Card>
      ))}
    </div>
  );
};

const BrandDetails = () => {
  const [review, setReview] = useState('');
  const query = new URLSearchParams(window.location.search);
  const sub = query.get('sub');

  const handleSubmitReview = async () => {
    if (!review) return alert('Please write a review before submitting.');
    try {
      const response = await fetch('/api/reviews', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ sub, review })
      });
      if (response.ok) {
        alert('Review submitted successfully!');
        setReview('');
      } else {
        alert('Failed to submit review. Please try again.');
      }
    } catch (error) {
      console.error('Error submitting review:', error);
      alert('An error occurred. Please try again later.');
    }
  };

  return (
    <Card className="p-4">
      <h2 className="text-2xl font-bold mb-4">{sub}</h2>
      <p className="mb-4">Details about {sub} will go here.</p>
      <h3 className="text-xl font-bold mb-2">Write a Review</h3>
      <textarea className='w-full p-2 border rounded mb-4' placeholder='Write your review...' value={review} onChange={(e) => setReview(e.target.value)} />
      <Button className='bg-blue-500 text-white p-2 rounded' onClick={handleSubmitReview}>Submit Review</Button>
    </Card>
  );
};

const BrandTree = () => {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<BrandTreeComponent />} />
        <Route path="/brand-details" element={<BrandDetails />} />
      </Routes>
    </Router>
  );
};

export default BrandTree;
