film tablosunda film uzunluğu length sütununda gösterilmektedir. Uzunluğu ortalama film uzunluğundan fazla kaç tane film vardır?
film tablosunda en yüksek rental_rate değerine sahip kaç tane film vardır?
film tablosunda en düşük rental_rate ve en düşün replacement_cost değerlerine sahip filmleri sıralayınız.
payment tablosunda en fazla sayıda alışveriş yapan müşterileri(customer) sıralayınız.

-- 1) Uzunluğu ortalamanın ÜSTÜNDE olan film sayısı
SELECT COUNT(*) AS count_above_avg_length
FROM film
WHERE length > (SELECT AVG(length) FROM film);

-- 2) En yüksek rental_rate değerine sahip film sayısı
SELECT COUNT(*) AS count_with_max_rental_rate
FROM film
WHERE rental_rate = (SELECT MAX(rental_rate) FROM film);

-- 3) En düşük rental_rate VE en düşük replacement_cost’a sahip filmler
SELECT film_id, title, length, rental_rate, replacement_cost
FROM film
WHERE rental_rate = (SELECT MIN(rental_rate) FROM film)
  AND replacement_cost = (SELECT MIN(replacement_cost) FROM film)
ORDER BY title;

-- 4a) En fazla sayıda alışveriş yapan müşteriler (tümü, çoktan aza sıralı)
SELECT c.customer_id, c.first_name, c.last_name, COUNT(*) AS payment_count
FROM payment p
JOIN customer c ON c.customer_id = p.customer_id
GROUP BY c.customer_id, c.first_name, c.last_name
ORDER BY payment_count DESC, c.customer_id;

-- 4b) En fazla sayıda alışveriş yapan müşteri(ler) (yalnızca en tepedekiler)
WITH counts AS (
  SELECT c.customer_id, c.first_name, c.last_name, COUNT(*) AS payment_count
  FROM payment p
  JOIN customer c ON c.customer_id = p.customer_id
  GROUP BY c.customer_id, c.first_name, c.last_name
)
SELECT *
FROM counts
WHERE payment_count = (SELECT MAX(payment_count) FROM counts)
ORDER BY customer_id;
