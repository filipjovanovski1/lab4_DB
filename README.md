# lab4_DB
1. SELECT u.name u.prezime FROM User u
JOIN Premium_korisnik pk ON u.username = pk.username
JOIN Recommendation r ON u.username = r.username_from
JOIN Video v ON r.title = v.title
WHERE v.duration > 120
AND r.rating >= 4
ORDER BY u.registration_date ASC; 

2. WITH UserRecommendations AS (
  SELECT k_ime_od, COUNT(*) AS recommendation_count 
  FROM Preporaka
  GROUP BY k_ime_od
  ORDER BY recommendation_count DESC 
  LIMIT 1
)
SELECT p.k_ime_na AS username, COUNT(p.naslov) AS number_of_videos
FROM Preporaka p 
JOIN UserRecommendations ur ON p.k_ime_od = ur.k_ime_od
GROUP BY p.k_ime_na;

3. SELECT p.ime as profile_name,
AVG(vr.avg_rating) AS average_wishlist_rating
FROM Profil p 
JOIN Lista_zelbi lz on p.k_ime = lz.k_ime
JOIN 
(SELECT naslov,
AVG(ocena) AS avg_rating
FROM Preporaka
GROUP BY naslov
) vr On lz.naslov = vr.naslov
GROUP BY p.ime

4. SELECT 
    vz.zanr AS genre,
    COUNT(*) AS recommendation_count
FROM 
    Preporaka p
JOIN 
    Video_zapis_zanr vz ON p.naslov = vz.naslov
WHERE 
    LOWER(p.komentar) LIKE '%interesting%'
GROUP BY 
    vz.zanr
ORDER BY 
    recommendation_count DESC;


5.SELECT vz.naslov AS title,
vz.vremetraenje AS duration,
COUNT(p.ID) as recommendation_count
FROM Video_zapis vz
JOIN Lista_zelbi lz on vz.naslov = lz.naslov
JOIN Preporaka p ON vz.naslov = p.naslov

where 
lz.naslov in (
  SELECT 
  lz.naslov
  FROM 
  Lista_zelbi lz
  GROUP BY
  lz.naslov
  HAVING
  count(DISTINCT lz.k_ime) >= 2
)
Group by 
vz.naslov, vz.vremetraenje
ORDER BY 
recommendation_count DESC;

6.SELECT DISTINCT k.korisnik_ime AS user_name
FROM Korisnik k
JOIN Preporaka p ON k.korisnik_ime = p.k_ime_od
WHERE NOT EXISTS (
    SELECT 1
    FROM Lista_zelbi lz
    WHERE lz.k_ime = k.korisnik_ime
      AND lz.naslov = p.naslov
);
