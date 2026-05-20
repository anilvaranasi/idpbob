### Responsive Design
1. Test on multiple screen sizes
2. Use Carbon's responsive breakpoints
3. Ensure mobile-friendly navigation
4. Optimize touch targets for mobile

## Carbon Design System Resources

### Official Documentation
- **IBM Carbon Design System:** https://carbondesignsystem.com/
- **Carbon Components:** https://www.carbondesignsystem.com/components/overview/
- **Carbon Design Kit:** https://www.carbondesignsystem.com/designing/kits/sketch/

### Color Tokens Reference
```css
/* UI Backgrounds */
--carbon-ui-01: #ffffff    /* Primary background */
--carbon-ui-02: #f4f4f4    /* Secondary background */
--carbon-ui-03: #e0e0e0    /* Tertiary background */
--carbon-ui-04: #8d8d8d    /* Border subtle */
--carbon-ui-05: #161616    /* Dark background */

/* Text Colors */
--carbon-text-01: #161616  /* Primary text */
--carbon-text-02: #525252  /* Secondary text */
--carbon-text-03: #a8a8a8  /* Placeholder text */
--carbon-text-04: #ffffff  /* Text on dark backgrounds */

/* Interactive Colors */
--carbon-interactive-01: #0f62fe  /* Primary button */
--carbon-link-01: #0f62fe         /* Links */
--carbon-focus: #0f62fe           /* Focus indicator */
```

## Integration with IDPBob Roles

The portal can be configured to work with the IDPBob roles:
- `x_ibmin_idpbob.application_developer`
- `x_ibmin_idpbob.platform_engineer`
- `x_ibmin_idpbob.sre`
- `x_ibmin_idpbob.security_engineer`
- `x_ibmin_idpbob.product_owner`
- `x_ibmin_idpbob.servicenow_admin`

### Role-Based Access Control
Configure page and widget access based on roles:
1. Navigate to **Service Portal > Pages**
2. Open a page record
3. In the **Roles** field, add required roles
4. Save the record

## Troubleshooting

### Theme Not Applying
1. Clear browser cache
2. Verify theme is selected in portal record
3. Check for CSS conflicts in browser console
4. Ensure update set was committed successfully

### Header/Footer Not Showing
1. Verify header/footer records are assigned to portal
2. Check that records are in the correct scope
3. Review template syntax for errors
4. Test in incognito/private browsing mode

### Navigation Links Not Working
1. Verify page IDs exist
2. Check user has access to linked pages
3. Review URL parameters
4. Test with different user roles

## Support and Maintenance

### Regular Updates
1. Monitor Carbon Design System updates
2. Review ServiceNow platform upgrades
3. Test portal after instance upgrades
4. Update documentation as needed

### Performance Monitoring
1. Track page load times
2. Monitor widget performance
3. Review browser console for errors
4. Analyze user feedback

## Next Steps

1. **Add Content Widgets:** Populate the homepage with relevant widgets
2. **Create Additional Pages:** Build catalog, knowledge base, and request pages
3. **Configure Search:** Set up portal search functionality
4. **Add Branding:** Customize logo and brand colors
5. **Set Up Analytics:** Track portal usage and user behavior
6. **Create User Documentation:** Provide end-user guides
7. **Implement Feedback:** Add user feedback mechanisms
8. **Test Accessibility:** Conduct thorough accessibility testing

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-05-01 | Initial portal creation with Carbon theme |

## Contact

For questions or issues related to the IDPBob Service Portal, contact the IDPBob development team.

---
**Last Updated:** 2026-05-01  
**Version:** 1.0.0  
**Framework:** ServiceNow ESC (Employee Service Center)  
**Design System:** IBM Carbon Design System v11